To deploy nexus we have to use the manifest files specified in this folder.
You can also find the original (unedited) manfifests in my email (spam)
I have ommitted some config maps and thus some volumes has been ommited for this deployment to work because we dont have the config map files
Notice that nexus comes with a delta.conf file
this is a config map that we have to apply in our cluster using the kustomization file specified
Kustomize is a Kubernetes configuration transformation tool that enables you to customize untemplated YAML files,
leaving the original files untouched. Kustomize can also generate resources such as ConfigMaps and Secrets from other representations.

Thus with kustomization, you can make changes on a kubernetes object without actually editing the original manifest file
You can have a group of kubernetes manifests eg. deploy.yaml, service.yaml all sitting in the same directory.
now this is the power of kustomization,
you can create a kustomization file in the same folder as the manifest files
You can have a resource tab in your kustomization file
resource: 
- deploy.yaml
- service.yaml

commonLabels: (commonAnnotation, nameprefix etc)
  app: myapp

if yoyu apply this customization, it will make changes on all the files you have added to its resource tab
provided they are all in the same folder/directory
thus, you can ommit the labels tab in your manifests and because of the commonlabels,
the label specified below will be added to all the manifests. 

Also another use case of kustomization is that you can use it to apply a secret or a config file.
in our case we have delta.conf
now we created a kustomization file that sits in the same directory as the config file.
recall that the config file might be in json or key value pair format and thus it shouldnt look like a manifest.
---
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: bbp
generatorOptions:
  disableNameSuffixHash: true # this flag is used to avoid creation of new configmap, instead it will be modified when file context is changed
configMapGenerator:
  - name: delta-config
    files:
      - delta.conf
---
You can use the config map generator or secret generator to create a secret out of the json or config file we have



