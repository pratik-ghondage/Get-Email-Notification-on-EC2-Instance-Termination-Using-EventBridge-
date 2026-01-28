# Get Email Notification on EC2 Instance Termination Using EventBridge

In this project, I implemented an event-driven notification system that sends an email alert whenever an EC2 instance is terminated.  
This is achieved using Amazon EventBridge and Amazon SNS, without writing any code.  
This type of setup is very useful for monitoring, cost control, and security auditing in AWS environments.

---

## Architecture Overview

The architecture follows an event-driven design using Amazon EventBridge and Amazon SNS to detect EC2 termination events and send email notifications automatically.

![EC2 Termination Notification Flow](./images/Email%20Notification%20on%20EC2%20Instance%20Termination.png)

---

## 1. Event Layer — EC2 State Change

At the Event layer, the source of the event is an EC2 instance.

- Whenever an EC2 instance changes its state, AWS automatically generates an event.
- In this project, the focus is on the instance state-change notification where the state becomes terminated.
- This event is published to the default EventBridge event bus.

**Key Point:** No manual trigger is required. The event is generated natively by AWS.

---

## 2. Rule Layer — EventBridge Rule

The Rule layer is handled by Amazon EventBridge.

- An EventBridge rule named `terminate_instance` is created.
- The rule listens to events from:
  - AWS Service: EC2  
  - Event type: EC2 instance state-change notification  
  - Specific state: terminated  
- The rule acts as a filter, ensuring that only termination events are processed.

**Key Point:** The rule decides when and which events should trigger an action.

---

## 3. Target Layer — SNS Topic

The Target layer is implemented using Amazon SNS.

- The EventBridge rule forwards the matched event to an SNS topic named `Global_topic`.
- SNS fan-outs the notification to its subscribers.
- In this project, the subscriber is an email endpoint.

**Key Point:** SNS is responsible for delivering the notification, not detecting the event.

---

## Step-by-Step Implementation

### Step 1: Create SNS Topic (Pre-requisite)

Before configuring EventBridge, an SNS topic was created to receive notifications.

- Open Amazon SNS
- Create a topic named `Global_topic`
- Create an email subscription
- Confirm the subscription from the email received

This SNS topic will act as the notification endpoint for EC2 termination events.

![SNS Topic and Email Subscription](images/SNS_topic.png)

---

### Step 2: Create EventBridge Rule with Event Pattern

- Open Amazon EventBridge
- Navigate to Event buses
- Select the default event bus
- Click Create rule

**Rule Configuration**
- Rule name: `terminate_instance`
- Event bus: Default
- Rule type: Rule with an event pattern
- Creation method: Use pattern form

**Event Pattern Details**
- Event source: AWS events / EventBridge
- AWS service: EC2
- Event type: EC2 instance state-change notification
- Specific state: terminated
- Instance: Any instance

This configuration ensures that the rule triggers only when an EC2 instance enters the terminated state.

![EventBridge Rule Configuration](images/Event_Bridge.png)

---

### Step 3: Add Target to the Rule

- Target type: AWS service
- Target service: SNS
- SNS topic: `Global_topic`

Once the rule is triggered, the event is forwarded to the SNS topic.

---

### Step 4: Test the Configuration

- Launch a new Amazon EC2 instance
- Terminate the instance manually
- Verify that an email notification is received

**Result:** Email alert is successfully delivered on EC2 instance termination.

![Email Notification Output](images/Email.png)

---

## Conclusion

This project demonstrates how Amazon EventBridge and Amazon SNS can be combined to build a simple yet powerful monitoring solution.  
Without writing a single line of code, we can track critical infrastructure events and receive real-time notifications.
