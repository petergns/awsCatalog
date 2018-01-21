Placeholder Draft

Create an AWS account

Create a Ubuntu Linix-based instance on Lightsail [1]
https://lightsail.aws.amazon.com/ls/docs/getting-started/article/getting-started-with-amazon-lightsail

1. Create new instance i.e. my_instance_name on Lightsail
2. Create and attach a static ip for your Lightsail instance i.e. my_static_ip
3. Note the Public and Private IPs for your instance, and add the following ports:

Static/Public IP: `18.216.39.42`

| Application   | Protocol      | Port Range  |
| ------------- |:-------------:| -----------:|
| SSH           | TCP           | 22          |
| HTTP          | TCP           | 80          |
| CUSTOM        | TCP           | 123         |
| CUSTOM        | TCP           | 2200        |
| CUSTOM        | TCP           | 5000        |
| CUSTOM        | TCP           | 8000        |

4. Find the DNS Address of your Instance. 

There are several sites that allow reverse IP lookup to allow you to do this. [2]

In the case of this instance it is: http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com





