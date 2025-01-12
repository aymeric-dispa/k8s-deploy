# How to deploy an app to running K8s cluster ???
I followed the tutorial https://www.red-gate.com/simple-talk/devops/containers-and-virtualization/deploying-a-dockerized-application-to-the-kubernetes-cluster-using-jenkins/ 
But unfortunately it was incomplete in the end !
See how I set it up below
# Setting Up Jenkins to Deploy to Minikube

This guide describes how to set up Jenkins to deploy workloads to a **Minikube cluster** using a **ServiceAccount** for authentication.

## Steps to Set Up

### 1. Create a ServiceAccount for Jenkins

Create a **ServiceAccount** in Kubernetes that Jenkins will use to authenticate:

```bash
kubectl create serviceaccount jenkins
```

Bind the `jenkins` ServiceAccount to the `cluster-admin` role (for full permissions):

```bash
kubectl create clusterrolebinding jenkins-binding \
--clusterrole=cluster-admin \
--serviceaccount=default:jenkins
```

---

### 2. Create a Secret/Token for the ServiceAccount

Since Kubernetes 1.24+, tokens are not automatically created for ServiceAccounts, so you need to create one manually:

1. Create a YAML file (`jenkins-token.yaml`) with the following content:

```yaml
apiVersion: v1
kind: Secret
metadata:
name: jenkins-token
annotations:
kubernetes.io/service-account.name: jenkins
type: kubernetes.io/service-account-token
```

2. Apply the YAML file:

```bash
kubectl apply -f jenkins-token.yaml
```

3. Retrieve the token from the secret:

```bash
kubectl describe secret jenkins-token
```

Copy the token from the output and create a new credentials in Jenkins. I named mine 'jenkins-token'

---

### 3. Retrieve the Kubernetes API URL

Get the Kubernetes API server URL:

```bash
kubectl cluster-info
```

You should see something like this:

```plaintext
Kubernetes control plane is running at https://127.0.0.1:52119
```
Since Jenkins is running in a Docker container, use host.docker.internal (macOS/Windows) to allow the container to access the host machine’s API:

```
https://host.docker.internal:6443
```

So then you can just use this in the section in the jenkins file
````
        withKubeConfig([credentialsId: 'jenkins-token', serverUrl: 'https://host.docker.internal:52119']) {
````


# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)
# k8s-deploy
