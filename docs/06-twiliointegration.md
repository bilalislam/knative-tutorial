# Integrate with Twilio

You can use [Twilio](https://www.twilio.com/) to embed voice, VoIP, and messaging into applications. In this lab, we will use a [Webhook](https://www.twilio.com/docs/glossary/what-is-a-webhook) to reply to SMS messages sent to a Twilio phone number. The Webhook is going to be a Knative service running on GKE.

## Twilio Setup

You need to [create a Twilio account](https://www.twilio.com/try-twilio) and get a [Twilio phone number](https://www.twilio.com/docs/usage/tutorials/how-to-use-your-free-trial-account#get-your-first-twilio-phone-number). You need to make sure that the Twilio number is SMS enabled. 

In Twilio [console](https://www.twilio.com/console), click on the phone number and go to `Messaging` section. There's a Webhook defined for when a message comes in. We will change that to our Knative Service later:

![Twilio Webhook](./images/twilio-webhook.png)

## Create Twilio SMS handler

Follow the instructions for your preferred language to create a service to handle SMS messages to our Twilio number:

* [Create Twilio SMS Handler - C#](06-twiliointegration-csharp.md)
 

## Build and push Docker image

Build and push the Docker image (replace `{username}` with your actual DockerHub): 

```docker
docker build -t {username}/twilio:v1 .

docker push {username}/twilio:v1
```

## Deploy the Knative service

Take a look at the [service.yaml](../serving/twilio/service.yaml) file.

```yaml
apiVersion: serving.knative.dev/v1beta1
kind: Service
metadata:
  name: twilio
  namespace: default
spec:
  template:
    spec:
      containers:
        # Replace {username} with your actual DockerHub
        - image: docker.io/{username}/twilio:v1
```

After the container is pushed, deploy the app. 

```bash
kubectl apply -f service.yaml
```

Check that the service is created:

```bash
kubectl get ksvc twilio

NAME            AGE
twilio   21s  
```
## Test the service

We can finally test our service by sending an SMS to our Twilio number. We need to setup Twilio Webhook first.

In Twilio [console](https://www.twilio.com/console), click on the phone number and go to `Messaging` section. For Webhook defined for when a message comes in, change it to your Knative service name and NIP.IO domain:

![Twilio Webhook](./images/twilio-webhook-custom.png)

Now, you can send an SMS message to your Twilio number and you should get a reply back from the Knative service!

## What's Next?
[Deploy to Cloud Run](07-deploycloudrun.md)