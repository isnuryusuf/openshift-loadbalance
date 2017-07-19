# openshift-loadbalance
Testing Load Balance Apps on Openshift
## Installing OpenShift, Please Refer to: https://github.com/isnuryusuf/openshift-install/blob/master/openshift-origin-quickstart.md



*** CREATE PROJECT ***##
oc new-project load-balance --display-name='Cat and City' --description='Load Balance Example'


*** CREATE APP1 called, load-balance1 ***##
oc new-app --name='load-balance1' -l name='load-balance' php~https://github.com/StefanoPicozzi/load-balance.git -e SELECTOR=cats
oc expose service load-balance1 --name=load-balance1 -l name='load-balance'


*** CREATE APP2, called load-balance2 ***##
oc new-app --name='load-balance2' -l name='load-balance' php~https://github.com/StefanoPicozzi/load-balance.git -e SELECTOR=cities
oc expose service load-balance2 --name=load-balance2 -l name='load-balance'


*** CREATE load-balance1 ROUTING WITH WEIG 50:50 ***##
oc expose service load-balance1 --name='ab' -l name='load-balance'  
oc set route-backends ab load-balance1=50 load-balance2=50
oc annotate route/ab haproxy.router.openshift.io/balance=roundrobin 


*** TESTING 50:50 ***##
while true; do curl -s http://ab-load-balance.smartfintech.i3-cloud.com/item.php | grep "data/images" | awk '{print $5}' ; sleep 1; done | cut -d '/' -f3

cities
cats
cities
cats
cities
cats
cities
