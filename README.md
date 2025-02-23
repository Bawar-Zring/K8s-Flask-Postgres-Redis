# K8s-Flask-Postgres-Redis
Deployment Flask app with Postgres &amp; Redis using Kubernetes in Minikube cluster.
 
  ----------------------------------------------------------------------------------------------------------------------------------------------------
What i did?
- creating docker file for my app.py
- push docker image to docker hub
- creating deployment & service for (flask, postgres and redis)
- putting sensitive data to secrets file
- using configMap for environment variables
- creating Persistent Volume(pv) & bound it to Persistent Volume Claim(pvc)

  --------
How to run?
- make sure minikube up and running ``` minikube status ```, if not ``` minikube start```.
1. after clone this repo simply run this command: ``` kubectl apply -f k8s/ ```.
2. check three pods are running ``` kubectl get pods ```.
3. then we need to check those pods work well together by using /health end point using curl:
   - we need to run curl to /health endpoint in flask pod.
   - first get pod name ``` kubectl get pods ``` find one that start with **flask-somehash-somehash**.
   - then get the service ``` kubectl get svc ``` to get the ClusterIp of **flask** pod.
   - goto exec mode of flask pod ``` kubectl exec -it flask-somehash-somehash -- bash ```.
   - using curl ``` curl http://flask-ClusterIp:5000/health ``` you should get ``"status": "connected to both redis and database"``.
4. check PV work well:
   - again goto exec mode of flask pod ``` kubectl exec -it flask-somehash-somehash -- bash ```.
   - change directory to /data```cd /data``` then create new file ```echo 'persistent data' > test.txt'```.
   - delete flask pod ``` kubectl delete -f k8s/flask-app.yaml ```.
   - recreate it again ``` kubectl apply -f k8s/flask-app.yaml ```.
   - goto flask pod exec mode, check the file ```cat /data/test.txt```.
  
   -------
- in case my image not availabe in docker hub any more, you can build it by you self, run this command ```docker build -t [your-dockerhub-username]/[image-name]:[tag] .```
- then push the image to you docker hub, remember to change the container image in flask-app.yaml file to [your-dockerhub-username]/[image-name]:[tag], ```docker push [your-dockerhub-username]/[image-name]:[tag]```
   
