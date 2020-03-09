# **Portshift Orb for CircleCi**

## **What is the Portshift Orb?**
The Portshift Orb enables you to you to scan images for vulnerabilities, in CircleCI, and report the results to the Portshift server. It can also generate the Portshift ID for an image, based on the image hash and image metadata. Portshift uses this ID to track workloads in protected environments.

Portshift workload runtime security policies can use vulnerabilities scan results as a criteria for allow or blocking images from running in Portshift protected environments.

The scan engine is based on [Clair](https://github.com/quay/clair), though additional scan engines may be added, to enhance the vulnerabilities information. 

Portshift Orb is an easy way to get started with Portshift runtime security platform. Image  identity and the vulnerabilities scan results from your CI pipeline are sent to Portshift, where you can see them on the  Management console.

## **How to use the Portshift Orb**

### **Prerequisites**
1. Create a Portshift user account. Portshift is a commercial SaaS platform, so you'll need a license to create a user account, before you can use the orb (see [www.portshift.io](www.portshift.io)). When you receive the "New User" onboarding email, go to the Portshift user-portal (www.console.portshift.io) to enter your domain.

2. Once you have opened an account on Portshift, define a Service User account. 

   1. Go to the *System* page, and select the *Manage Users* tab.
   1. Create a new user, with the Service User role.
     ![new user](Images/New%20user%20image.png)
   1. Click on the Token icon for the new user, in the *Users* page.
       ![new user token](Images/New%20user%20token.png)
   1. Copy the *Access Key* and *Secret Key* (use the copy icon).
  
1. In CircleCI, navigate to Organization Settings> Context >Create new Context
       ![context](Images/Context.jpg)
1. Create two new CircleCI environment variables for the Portshift *Access Key* and *Secret Key* 
  
### **Add the Portshift Orb to your CircleCI pipeline**

Create a a directory where you will build your image. This will later be persisted to a [Workspace](https://circleci.com/docs/2.0/concepts/#workspaces-and-artifacts).

```- run: mkdir -p workspace ```

Build your image:

```- run: docker build -t <myrepo/myimage:tag>```


```
 - persist_to_workspace:
          root: workspace
          paths:
              - image.tar
```

Add the Portshift Orb:

`portshiftscanner: 'portshift/portshift-scanner@1.0.0`

Create a new job for Portshift Orb:

```
jobs:
      - docker-build
      - portshiftscanner/scan-image:
          requires:
            - docker-build
          context: portshift-credentials
          image_name: <image-name>
          image_tag: <image-tag>
          access_key: ${ACCESS_KEY}
          secret_key: ${SECRET_KEY}
```

### **Orb Output**

You can see the scan results in the job output.
   
   ![job output](Images/output.png)

The results are also sent to Portshift. View them in the *Images* tab of the *Workloads* page. You will see a new record with your image identity, and the vulnerabilities scan results.


