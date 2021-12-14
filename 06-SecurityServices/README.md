# Extra ball: Security Services

Every hyperscaler will provide you with a set of services that are useful for keeping your systems secure. Here you will find a short description and a related video for the most important ones offered by AWS (excluding the most network-related ones):

## CloudTrail

This is a fundamental one, as it acts like the Audit service for every single API call run against an account. Also, other types of events are recorded (like failed login attempts or logins performed by a root account).

It is possible to batch-process the events, as they are delivered as a log in S3 in periods of ten or fifteen minutes. But it is also possible to response in almost real-time by subscribing to signals generated in Event Bridge.

CloudTrail is activated on region basis, but global services (like IAM) must be tracked from Virginia.

Finally, best practices include to activate integrity checks for the logs and to automatically transfer all those logs to a security account as soon as they are available to prevent any manipulation.

[![CloudTrail intro](https://img.youtube.com/vi/4ztTv5rIRv8/0.jpg)](https://www.youtube.com/watch?v=4ztTv5rIRv8)

## Config

Once it is activated, changes in the configuration of the supported services are going to be tracked. It is possible to subscribe actions to those configurations, so (for example) if a particular security group is altered then a lambda function will be executed (to maybe revert the change).

Config includes some pre-made rules and remediations that can be applied, explained in a nice human-oriented language instead of json (for example, "all EBS volumes should be encrypted"). Those rules can be enforced or just used to generate compliance reports.

[![Config presentation](https://img.youtube.com/vi/X_fznJtSyV8/0.jpg)](https://www.youtube.com/watch?v=X_fznJtSyV8)


## VPC Flow Logs

It can be activated for any network card (or a whole subnet) and will record the communications generated or received on those devices. It will not keep the content of the traffic (there is another service called Traffic Mirroring for that) but will contain the origin, the destination and the size of the bytes transferred. Using that information, strange behaviours can be detected.

[![VPC Flow Logs demo](https://img.youtube.com/vi/sThQD7wjpgA/0.jpg)](https://www.youtube.com/watch?v=sThQD7wjpgA)

## GuardDuty

This service will apply Machine Learning models to find suspicious activity on your system. The analysis will be performed on several logs (CloudTrail, DNS logs, VPC Flow Logs) so it is important to understand that it will not affect the actual resources.

[![How do I identify SSH Brute force attacks](https://img.youtube.com/vi/nRs-Z41zTos/0.jpg)](https://www.youtube.com/watch?v=nRs-Z41zTos)


## Detective

This service will help you to investigate security threads, by correlating different logs. For example, once an attack has been detected from a particular IP to an EC2 instance, it will show the origin of the attack with the VPC Flow log related to that virtual machine.

[![Detective demo](https://img.youtube.com/vi/fmm4PXhg8BY/0.jpg)](https://www.youtube.com/watch?v=fmm4PXhg8BY)

## Inspector

This is an agent-based software vulnerability detection system. Once installed on a server, it will scan for known issues in the libraries and processes being run on it.

The service received a lot of attention during the last Re:Invent, with a revamped version that makes it much more appealing than before. Also, it can be deployed on-premises.y

[![Inspector talk by the great Darko](https://img.youtube.com/vi/tdQbovyLYYE/0.jpg)](https://www.youtube.com/watch?v=tdQbovyLYYE)

## Macie

This service will apply ~magic~ Machine Learning on massive amounts of data stored on S3 to find sensitive data, like API keys or personal information. The service used to be very expensive, but I have to confess I haven't followed its evolution during recent times.

[![Amazon Macie demo](https://img.youtube.com/vi/8piwEQJJXdo/0.jpg)](https://www.youtube.com/watch?v=8piwEQJJXdo)

## Security Hub

This service works as a [SIEM](https://en.wikipedia.org/wiki/Security_information_and_event_management) aggregator, both for visualizing findings from the internal AWS services and also integrating third party products from companies like Aqua Security, Atlassian, Check Point, etc.

One important point regarding this service is that it will easily provide an organizational-wide view of the system, including multiple accounts on the same panel.

[![Security Hub and Organizations](https://img.youtube.com/vi/wXkuRiNOo4o/0.jpg)](https://www.youtube.com/watch?v=wXkuRiNOo4o)
