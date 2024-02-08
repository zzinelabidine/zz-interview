### Hello Welcom to Fluxcd interview with Zied !!


As we see we have 3 repos :

apps/wp : application ressourses
clusters/minikube : fluxcd ressourses
wp-chart : app charts files 


### Helm charts 

1- I pushed my own image wp on my own registry on oci://public.ecr.aws/werollingupdate/wordpress:0.1.0

*ECR public*  

    image:
      registry: public.ecr.aws
      repository: werollingupdate/interview-zz 
      tag: v0.1.0   # {"$imagepolicy": "flux-system:wp:tag"}
      digest: ""


2- I created Helm charts wordpress with bitnami/wp  

I pushed this chart on my ECR charts registry public :

     url: oci://public.ecr.aws/werollingupdate/

        chart : wordpress 
        version: 0.1.0

#Evrey think is working with my cred you can tested

### FLUXCD

#### Fluxcd Part :


 1 - First connect Flucd with my repo github with boostrap cli and CRED $GITHUB_USER && $GITHUB_TOKEN  :



    flux bootstrap github \
      --components-extra=image-reflector-controller,image-automation-controller \
      --token-auth \
      --owner=zzinelabidine \
      --repository=zz-interview \
      --branch=main \
      --path=clusters/minikube \
      --read-write-key \
      --personal


2-  Create CD with the automation deploy helmrelease with tag deploy on registry image when push is done   : 


* Gitrepository : to detect any change on my own branch main only on folder apps/wp all the **5 min** than apply kustomise to deploy my helmrelease with myvalues!!


* Imagerepo : To detect any change on my public registry : **public.ecr.aws/werollingupdate/interview-zz** **evrey 5m**

* ImagePolicy : to get tags more than > 0.1.x

* kustomise to tell fluxcd to created my release with the new values (where its will change the image tag) where a the new tag is pushed :

  **tag: v0.1.0   # {"$imagepolicy": "flux-system:wp:tag"}** syntaxe to tell fluxcd to change tag with the new one 


  For this i specified :

  HelmRepository :


      type: oci
      url: oci://public.ecr.aws/werollingupdate/
      secretRef:
      name: docker-config
      
      And my helmrelease with 

      valuesFrom:
        - kind: ConfigMap
          name: wp-values


For apply this , with kubectl apply -f 'Fluxcdfile.yaml'

For test : docker tag public.ecr.aws/werollingupdate/interview-zz:v0.1.0 public.ecr.aws/werollingupdate/interview-zz:v0.1.10

thanks ou for you attention .
