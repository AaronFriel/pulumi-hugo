---
title: "Access Control"
layout: topic

# The date represents the date the course was created. Posts with future dates are visible
# in development, but excluded from production builds. Use the time and timezone-offset
# portions of of this value to schedule posts for publishing later.
date: 2021-09-15T12:20:24-05:00

# Draft posts are visible in development, but excluded from production builds.
# Set this property to `false` before submitting the topic for review.
draft: true

# The description summarizes the course. It appears on the Learn home and module index pages.
description: Learn how to use Pulumi Crosswalk for Kubernetes to enforce RBAC.

# The meta_desc property is used for targeting search results or social-media previews.
meta_desc: Learn how to use Pulumi Crosswalk for Kubernetes to enforce RBAC.

# The order in which the topic appears in the module.
index: 2

# The estimated time, in minutes, for new users to complete the topic.
estimated_time: 10

# The meta_image appears in social-media previews and on the Learn Pulumi home page.
# A placeholder image representing the recommended format, dimensions and aspect
# ratio has been provided for reference.
meta_image: meta.png

# The optional meta_video also appears in social-media previews (taking precedence
# over the image) and on the module's index page. A placeholder video representing
# the recommended format, dimensions and aspect ratio has been provided for reference.
# meta_video:
#     url: 'http://destination-bucket-568cee2.s3-website-us-west-2.amazonaws.com/video/2020-09-03-16-46-41.mp4'
#     thumb: 'http://destination-bucket-568cee2.s3-website-us-west-2.amazonaws.com/thumbs/2020-09-03-16-46-41.jpg'
#     preview: 'http://destination-bucket-568cee2.s3-website-us-west-2.amazonaws.com/previews/2020-09-03-16-46-41.jpg'
#     poster: 'http://destination-bucket-568cee2.s3-website-us-west-2.amazonaws.com/posters/2020-09-03-16-46-41.jpg'

# At least one author is required. The values in this list correspond with the `id`
# properties of the team member files at /data/team/team. Create a file for yourself
# if you don't already have one.
authors:
    - kat-cosgrove

# At least one tag is required. Lowercase, hyphen-delimited is recommended.
tags:
    - learn
    - access-control
    - rbac
# When provided, links are rendered at the bottom of the topic page.
links:
    - text: Some Website
      url: http://something.com

# Exclude from search-engine indexing for now.
block_external_search_index: true
---

Say you want to allow teammates to make changes to your Boba Shop app, but you want to control who has access to specific aspects of it. Using [Pulumi Crosswalk](https://www.pulumi.com/docs/guides/crosswalk/kubernetes/), configuring RBAC ([Role Based Access Control](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)) is a little less painful. This module will focus on Crosswalk for Kubernetes, but we also provide [Crosswalk for AWS](https://www.pulumi.com/docs/guides/crosswalk/aws/).

{{< chooser cloud "aws,azure,gcp" / >}}

{{% choosable cloud aws %}}

Access control in Kubernetes is done by configuring permissions for IAM users
and roles to operate in the cluster.

The `kubeconfig` will be shared across users for access, and each IAM role
will have a particular binding into the cluster's auth to determine how it works
with the cluster.

The full code for this stack is on [GitHub][gh-repo-stack].

<!-- markdownlint-disable url -->
[gh-repo-stack]: https://github.com/pulumi/kubernetes-guides/tree/master/aws/03-cluster-configuration
<!-- markdownlint-enable url -->

{{% /choosable %}}

{{% choosable cloud azure %}}

Access control in Kubernetes is done by configuring permissions for Azure Active Directory (AD) users
and groups to operate in the cluster.

The `kubeconfig` will contain user authentication tokens for access, and each AD group
will have a particular binding into the cluster's auth to determine how it works
with the cluster.

The full code for this stack is on [GitHub][gh-repo-stack].

<!-- markdownlint-disable url -->
[gh-repo-stack]: https://github.com/pulumi/kubernetes-guides/tree/master/azure/03-cluster-configuration
<!-- markdownlint-enable url -->

{{% /choosable %}}

{{% choosable cloud gcp %}}

Access control in Kubernetes is done by configuring permissions for IAM
ServiceAccounts to operate in the cluster.

The `kubeconfig` will be shared across ServiceAccounts for access, and each
ServiceAccount will have a particular binding into the cluster's auth to determine how it works
with the cluster.

The full code for this stack is on [GitHub][gh-repo-stack].

<!-- markdownlint-disable url -->
[gh-repo-stack]: https://github.com/pulumi/kubernetes-guides/tree/master/gcp/03-cluster-configuration
<!-- markdownlint-enable url -->

{{% /choosable %}}

## Configure RBAC Authorization

{{% choosable cloud azure %}}

Access control is not configured by default for **non-admins** with Kubernetes RBAC.

In [Identity][crosswalk-identity] developers can **authenticate** into the cluster
using the kubeconfig, but they are not yet **authorized** to do work in the cluster.

This means that the user cannot perform any operations in the cluster by
default, such as retrieve information, as shown in the `Error from server (Forbidden)`
messages.

```bash
$ az login --service-principal --username $ARM_CLIENT_ID --password $ARM_CLIENT_SECRET --tenant $ARM_TENANT_ID
$ export KUBECONFIG=`pwd`/kubeconfig-devs.json
$ kubectl run --namespace=`pulumi stack output appsNamespaceName` --generator=run-pod/v1 nginx --image=nginx --port=80 --expose --service-overrides='{"spec":{"type":"LoadBalancer"}}' --limits cpu=200m,memory=256Mi
Error from server (Forbidden): pods is forbidden: User "pulumi:alice" cannot create resource "pods" in API group "" in the namespace "apps-x1z818eg"
```

Below is an example of how to create a Kubernetes Role and RoleBiding for the `devs` to **only** deploy common
workloads in the `apps` namespace (created in  [cluster defaults][crosswalk-configure-defaults]).

Assume the `admin` user.

```ts
$ az login --service-principal --username $ARM_CLIENT_ID --password $ARM_CLIENT_SECRET --tenant $ARM_TENANT_ID
$ export KUBECONFIG=`pwd`/kubeconfig-admin.json
```

<!-- markdownlint-disable url -->
[crosswalk-configure-defaults]: {{< relref "/docs/guides/crosswalk/kubernetes/configure-defaults" >}}
[crosswalk-identity]: {{< relref "/docs/guides/crosswalk/kubernetes/identity" >}}
<!-- markdownlint-enable url -->

{{% /choosable %}}

{{% choosable cloud aws %}}

Access control is not configured by default for **non-admins** with Kubernetes RBAC.

In [Identity][crosswalk-identity] developers can **authenticate** into the cluster
using the IAM role and kubeconfig, but it is not yet **authorized** to do work in the cluster.

This means that the user cannot perform any operations in the cluster by
default such as retrieve information, as shown in the `Error from server (Forbidden)`
messages.

```bash
$ aws sts assume-role --role-arn `pulumi stack output devsIamRoleArn` --role-session-name k8s-devs
$ export KUBECONFIG=`pwd`/kubeconfig-devs.json
$ kubectl run --namespace=`pulumi stack output appsNamespaceName` --generator=run-pod/v1 nginx --image=nginx --port=80 --expose --service-overrides='{"spec":{"type":"LoadBalancer"}}' --limits cpu=200m,memory=256Mi
Error from server (Forbidden): pods is forbidden: User "pulumi:alice" cannot create resource "pods" in API group "" in the namespace "apps-x1z818eg"
```

Below is an example of how to create a Kubernetes `Role` with the ability to **only** deploy common
workloads in the `apps` namespace (created in [cluster defaults][crosswalk-configure-defaults]). We also create a `RoleBinding`
to associate the Kubernetes `Role` to the IAM `devs` role.

Assume the `admin` user.

```bash
$ aws sts assume-role --role-arn `pulumi stack output adminsIamRoleArn` --role-session-name k8s-admins
$ export KUBECONFIG=`pwd`/kubeconfig-admin.json
```

<!-- markdownlint-disable url -->
[crosswalk-configure-defaults]: {{< relref "/docs/guides/crosswalk/kubernetes/configure-defaults" >}}
[crosswalk-identity]: {{< relref "/docs/guides/crosswalk/kubernetes/identity" >}}
<!-- markdownlint-enable url -->

{{% /choosable %}}

{{% choosable cloud gcp %}}

In [Identity][crosswalk-identity], access control is configured on the ServiceAccounts to bind to
[Predefined GKE Roles][gke-predefined-roles] for GKE managed Kubernetes RBAC.

If you wish to not bind a ServiceAccount to predefined GKE roles,
the user will be able to **authenticate** into the cluster, but they will not be
**authorized** to do work until given permissions.

This means that the user cannot perform any operations in the cluster by
default such as retrieve information, as shown in the `Error from server (Forbidden)`
messages.

```bash
$ gcloud auth activate-service-account --key-file k8s-devs-sa-key.json
$ export KUBECONFIG=`pwd`/kubeconfig.json
$ kubectl run --namespace=`pulumi stack output appsNamespaceName` --generator=run-pod/v1 nginx --image=nginx --port=80 --expose --service-overrides='{"spec":{"type":"LoadBalancer"}}' --limits cpu=200m,memory=256Mi
Error from server (Forbidden): pods is forbidden: User "pulumi:alice" cannot create resource "pods" in API group "" in the namespace "apps-x1z818eg"
```

As an example, if the `devs` ServiceAccount was not bound to the `roles/container.developer` permission,
an `admin` would need to create [Kubernetes RBAC][k8s-rbac-docs] resources to bind the ServiceAccount to certain privileges.

Below is an example of how to create a Kubernetes `Role` with the ability to **only** deploy common
workloads in the `apps` namespace (created in [cluster defaults][crosswalk-configure-defaults]), and a `RoleBinding` to associate
the Kubernetes `Role` to the IAM `devs` role.

<!-- markdownlint-disable url -->
[crosswalk-configure-defaults]: {{< relref "/docs/guides/crosswalk/kubernetes/configure-defaults" >}}
[k8s-rbac-docs]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[gke-predefined-roles]: https://cloud.google.com/kubernetes-engine/docs/how-to/iam#predefined
[crosswalk-identity]: {{< relref "/docs/guides/crosswalk/kubernetes/identity" >}}
<!-- markdownlint-enable url -->

Assume the `admin` user.

```bash
$ gcloud auth activate-service-account --key-file k8s-admin-sa-key.json
$ export KUBECONFIG=`pwd`/kubeconfig-admin.json
```

{{% /choosable %}}

Create a Role and RoleBinding for the `pulumi-devs` group in the `apps`
Namespace.

```typescript
import * as k8s from "@pulumi/kubernetes";

// Create a limited role for the `pulumi:devs` to use in the apps namespace.
let devsGroupRole = new k8s.rbac.v1.Role("pulumi-devs",
    {
        metadata: {namespace: appNamespaceName},
        rules: [
            {
                apiGroups: [""],
                resources: ["pods", "secrets", "services", "persistentvolumeclaims"],
                verbs: ["get", "list", "watch", "create", "update", "delete"],
            },
            {
                apiGroups: ["extensions", "apps"],
                resources: ["replicasets", "deployments"],
                verbs: ["get", "list", "watch", "create", "update", "delete"],
            },
        ],
    },
    {provider: cluster.provider},
);

// Bind the `pulumi:devs` RBAC group to the new, limited role.
let devsGroupRoleBinding = new k8s.rbac.v1.RoleBinding("pulumi-devs", {
    metadata: {namespace: appNamespaceName},
    subjects: [{
        kind: "Group",
        name: "pulumi:devs",
    }],
    roleRef: {
        kind: "Role",
        name: devsGroupRole.metadata.name,
        apiGroup: "rbac.authorization.k8s.io",
    },
}, {provider: cluster.provider});
```

{{% choosable cloud aws %}}

After creating the Role and RoleBinding in a Pulumi update, now try
deploying the workload using the `devs` role.

```bash
$ aws sts assume-role --role-arn `pulumi stack output devsIamRoleArn` --role-session-name k8s-devs
$ export KUBECONFIG=`pwd`/kubeconfig-devs.json
$ kubectl run --namespace=`pulumi stack output appsNamespaceName` --generator=run-pod/v1 nginx --image=nginx --port=80 --expose --service-overrides='{"spec":{"type":"LoadBalancer"}}' --limits cpu=200m,memory=256Mi
service/nginx created
pod/nginx created
```

{{% /choosable %}}

{{% choosable cloud azure %}}

After creating the Role and RoleBinding in a Pulumi update, now try
deploying the workload using the `devs` role.

```bash
$ az login --service-principal --username $ARM_CLIENT_ID --password $ARM_CLIENT_SECRET --tenant $ARM_TENANT_ID
$ export KUBECONFIG=`pwd`/kubeconfig-devs.json
$ kubectl run --namespace=`pulumi stack output appsNamespaceName` --generator=run-pod/v1 nginx --image=nginx --port=80 --expose --service-overrides='{"spec":{"type":"LoadBalancer"}}' --limits cpu=200m,memory=256Mi
service/nginx created
pod/nginx created
```

{{% /choosable %}}

{{% choosable cloud gcp %}}

After creating the Role and RoleBinding in a Pulumi update, now try
deploying the workload using the `devs` role.

```bash
$ gcloud auth activate-service-account --key-file k8s-devs-sa-key.json
$ export KUBECONFIG=`pwd`/kubeconfig.json
$ kubectl run --namespace=`pulumi stack output appsNamespaceName` --generator=run-pod/v1 nginx --image=nginx --port=80 --expose --service-overrides='{"spec":{"type":"LoadBalancer"}}' --limits cpu=200m,memory=256Mi
service/nginx created
pod/nginx created
```

{{% /choosable %}}

Delete the pod and service.

```bash
$ kubectl delete --namespace=`pulumi stack output appsNamespaceName` pod/nginx svc/nginx
```

{{% choosable cloud aws %}}

For a complete example of this in action, see [Simplifying Kubernetes
RBAC in Amazon EKS][simplify-rbac].
[simplify-rbac]: /blog/simplify-kubernetes-rbac-in-amazon-eks-with-open-source-pulumi-packages/

{{% /choosable %}}

See the [official Kubernetes RBAC docs][k8s-rbac-docs] for more details.

<!-- markdownlint-disable url -->
[crosswalk-identity]: {{< relref "/docs/guides/crosswalk/kubernetes/identity" >}}
[k8s-rbac-docs]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[crosswalk-configure-defaults]: {{< relref "/docs/guides/crosswalk/kubernetes/configure-defaults" >}}
<!-- markdownlint-enable url -->