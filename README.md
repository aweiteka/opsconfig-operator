# opsconfig-operator
Ansible operator to configure an OpenShift Dedicated cluster

## Run it

1. Clone this repo

        git clone https://github.com/aweiteka/opsconfig-operator.git
        cd opsconfig-operator
1. Create project
        oc new-project opsconfig
1. Apply objects

        oc create -f deploy/crds/ops_v1v1alpha1_opsconfig_crd.yaml
        oc create -f deploy/test/manifests.yaml
        oc create -f deploy/test/operator.yaml
        oc create -f deploy/crds/ops_v1v1alpha1_opsconfig_cr.yaml
1. Watch it deploy and do its magic. Inspect cluster for expected configuration.
1. Edit `deploy/crds/ops_v1v1alpha1_opsconfig_cr.yaml` file and apply changes.

        oc apply -f deploy/crds/ops_v1v1alpha1_opsconfig_cr.yaml

## Hack it

**Prerequisite:** Steps #1 #2 above

1. Create objects. NOTE: we will not be creating the `operator.yaml` object.

        oc create -f deploy/crds/ops_v1alpha1_opsconfig_crd.yaml
        oc create -f deploy/test/manifests.yaml
1. Modify `watches.yaml` file to point to local playbook.yaml file in this repo

          role: /path/to/my/playbook.yaml
1. Create a development environment following the [ansible operator user guide](https://github.com/operator-framework/operator-sdk/blob/master/doc/ansible/user-guide.md).

        operator-sdk up local --namespace opsconfig
1. Create CR object.

         oc create -f deploy/test/ops_v1alpha1_opsconfig_cr.yaml
1. Modify roles and apply to minishift environment
1. Test until satisfied
1. Revert role path change in `watches.yaml`
1. build and push the image

        operator-sdk build quay.io/aweiteka/opsconfig-operator:v0.0.4
        docker push quay.io/aweiteka/opsconfig-operator:v0.0.4

## Troubleshooting

When developing playbooks run them with `ansible-playbook`. Getting output from a failed `operator-sdk` job is a pain ([resolution](https://github.com/operator-framework/operator-sdk/pull/713)). This error has very little information:

```
ERRO[0059] error from ansible-runner: exit status 2      component=runner job=3747895247646406351 name=post-install namespace=opsconfig
```

The ansible stdout is found at this path on the workstation running `operator-sdk`. This is very challenging to get to when running in a pod.

```
/tmp/ansible-operator/runner/ops.openshift.com/v1alpha1/Opsconfig/opsconfig/post-install/artifacts/<JOB>/stdout
```

## TODO

Configuration roles that may fit this model:

1. ops_roles/alertmanager_config
1. tools_roles/openshift_users
1. tools_roles/openshift_master_resource_quota
1. tools_roles/openshift_master_audit_config
1. ops_roles/openshift_master_config
1. tools_roles/openshift_user_policy
1. ops_roles/openshift_scc
1. ops_roles/openshift_custom_registry_pullthrough_secret
1. https://github.com/openshift/online/tree/master/ansible/roles/oso_dedicated_admin

**Outstanding Questions**

* What is the impact of frequent runs from ansible-runner? Is there a way to get it to watch primary resources properly?
