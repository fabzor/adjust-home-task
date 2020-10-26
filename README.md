# take-home-task
Solutions of the two home tasks assigned for the "Network Monitoring Engineer" application at Adjust.

* *Task 1: please design a script that writes the numbers from 1 - 10 in random order. Each number should appear only once. You can use bash only. Please provide tests for the script, along with documentation which should include build instructions, usage, description, known limitations / bugs.*

* *Task 2: imagine a server with the following specs: 4 times Intel(R) Xeon(R) CPU E7-4830 v4 @ 2.00GHz, 64GB of ram, 2 tb HDD disk space, 2 x 10Gbit/s nics.
The server is used for SSL offloading and proies around 25000 requests per second. Please let us know which metrics are interesting to monitor in that specific case and how would you do that? What are the challenges of monitoring this?*

# Solution 1 - Randomize a list of numbers

This script will print all the numbers from 1 to 10 in a random order and can be used on any Linux distribution.

## How to use it

0. Clone this repo in your local environment:
```
git clone https://github.com/fabtests/test-tasks.git
```

1. Navigate to the scripts folder:
```
cd scripts
```

2. Grant permissions to execute your script:
```
chmod +x randomize.sh
```

3. Execute the script:
```
./randomize.sh
```

## Example of usage

```
$ ./randomize.sh
Here is a randomized sequence of all numbers from 1 to 10:
5
10
3
7
1
8
9
4
2
6
```

## Known limitations

This script is not generic, as it only works on the 1-10 interval.

# Solution 2 - Server monitoring

Here are listed some important server metrics for this specific case, choosing Zabbix for network monitoring.

* CPU utilisation and load
* MEMORY/CPU utilisation of processes of interest (for instance, processes using OpenSSL)
* Temperature
* Outgoing/incoming traffic on network interface
* Statistics of tcp/udp (netstat)
* Response time (ping)
* Server uptime
* Disk space

*Practically, one way to monitor these metrics can be Zabbix - most of these are available as prebuilt item types (including SNMP agents) making their implementation easier, so that we are able to focus more on the monitoring/alerting logic.*

* SSL Monitoring: check validity and expiration date of SSL certificates

*Practically, define a script to check SSL certificates with the remote hosts (OpenSSL), then implement the scheduled execution on Zabbix as an item.*

* Requests: get the number of requests from the access log
* Denied requests, connection errors: these metrics could be provided directly by the proxy service

*Practically, process these metrics on Zabbix as a delta type to get them in form of data per second (if we can't fetch them in that form).*

DIFFICULTIES:
* Collecting alot of data for monitoring can be inefficient; things that should be considered in this regard could be bulk-collecting data before processing it, limiting the size of the logs, understanding how often every single metric should be considered
* Understanding when the warnings are really necessary (e.g. for connection errors metrics one should evaluate how many can be tolerated, so that the warning is not triggered in vain)
* Being sure that our monitoring system is actually working with the architecture of our system (e.g. in case of Zabbix, using active agents if the server is hidden behind a firewall)

