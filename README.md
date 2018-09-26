# kubernetes-ingress-expose
expose your kubernetes deployment in IBM Cloud using ingress
## Description

## Procedure
### Host your service
1. Create the kubernetes deployment on IBM Cloud by creating a namespace first and then configurating your local machine to your initialized cluster on the cloud.
2. Write a **deploy.yml** file that contains basic configuration for your kubernetes cluster. Specify the container you want to clone and deploy from docker repository. Sample deploy.yml file is given for reference.
3. Write a **service.yml** file to use the exposed port of your docker image to your container registry. Port is where your application will be hosted. NodePort is going to act as an exposed port to outside communication(not recommended because of safety). Targetport is going to specify the exposed port of your application(as defined by the dockerfile of your image).
4.Apply/Create kubernetes configuration file on the Cloud by using the following command.

### Configure Ingress
1.Write a **ingress.yml** file to make your application accessible to the outside world.
  - Note:
    - To expose an app by ingress, you must craete a Kuberentes service for your app and register this service with Ingress by defining an Ingress Resource.
    - Ingress Resource especifies the path to your app services, which are appedned to the public route to form a unique app URL such as mycluster.us-east.containers.appdomain.cloud/myapp1
    - One ingress service is required per namespace.

2. Add a label to your metadata deployment so taht pods can be identified explicitly and Ingresss load balancing can be assoicated with it.
3. Create a sercie configfile that exposes kubernetes app/pod/deploy to a cluster's ALB in Ingress Load Balancer.
4. For apps hosted on your own IBM Cloud container, you need to specify the IBM provided domain such as;<br>
`
mycluster.us-east.containers.appdomain.cloud
`

3. Fetch the IBM provided domain using the following command.<br/>
`ibmcloud ks cluster-get mycluster | greb Ingress`
 
4. Create an **Ingress.yml** for your application service.<br/>
   Note: If the service redirects to an another link then mention it under annotation so that it can be rewritten.<br>
   
```
 .
 .
 .
 metadata:
   name: elkibana-ingress
   annotations:
     ingress.bluemix.net/rewrite-path: "serviceName=elkibana rewrite=/app/kibana"
.
.
.
```
  Here, our application takes us to `/app/kibana` path when we access it normally. In order to make it accessable at `/` to above definition is required.
  
5. In the `.yml`, especify the IBM-provided host and the path that you want to append at the end of that host. This will explicitly redirect traffic to your specified path.

6. Apply your ingress resource.<br>
`kubectl apply -f ingress.yml -n <namespace>`

7. To verify successful association of Ingress, use the following command.<br>
`kubectl describe  ingress myingressresource`

8. Access your application at `<domain>/<path>`.
