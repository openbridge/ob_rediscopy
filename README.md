![Redis](./images/redis.png)


# Redis Backup

## Overview

You have one or more Redis instances that you want to backup. However, you want a backup that is in a format that is more flexible than the standard `.rdb` or `.aof` file.

Having your data as `JSON` would be ideal so this Docker images exports or imports Redis data sets into that format.


## Configuration

### Environment Variables

There are currently 6 variables that need to be set to run the backup container.
* **redisoperation**: There are two modes that can be set for redisoperation: "IMPORT" or "EXPORT". IMPORT allows you to import a backup set of keys while EXPORT allows you to dump all the keys into a JSON file.
* **rediskeydir**: Where do you want to import from or export (i.e., "/path/to/dir/")?
* **rediskeyfile**: What is the name you want to use for the IMPORT or EXPORT redisoperation (i.e. "nameofkey.json")?
* **redisdb**: What is the database you need to connect (e.g., This typically would be 0, but it may be others depending on how it was setup)?
* **redishost**: What is the hostname or IP address of the Redis instance?
* **redisport**: What port is needed for the Redis instance?

## Usage Examples
The following are a set of example commands:
##### Export
This is an example of running an `EXPORT` operation:
```bash
docker run -v /ebs/data:/ebs/data  -e redisoperation="export" -e rediskeydir="/ebs/data/backup/redis/" -e rediskeyfile="foo_redisbackup.json" -e redishost="172.17.0.1" -e redisport="6379" -e redisdb="0" 791778434480.dkr.ecr.us-east-1.amazonaws.com/openbridge/redisbackup
```
##### Import
This is an example of running an `IMPORT` operation:
```bash
docker run -v /ebs/data:/ebs/data -e redisoperation="import" -e rediskeydir="/ebs/data/backup/redis/" -e rediskeyfile="foo_redisbackup.json" -e redishost="172.17.0.1" -e redisport="6379" -e redisdb="0" 791778434480.dkr.ecr.us-east-1.amazonaws.com/openbridge/redisbackup
```

#### Cron
You can set Redis Backup to run via `Cron`. The following example will backup Redis once every 30 minutes:
```bash
*/30 * * * * /usr/bin/bash -c 'docker run -v /ebs:/ebs -e redisoperation="export" -e rediskeydir="/ebs/data/backup/redis/" -e rediskeyfile="`date +\%Y\%m\%d\%H\%M\%S`_redisbackup.json" -e redishost="172.17.0.1" -e redisport="6379" -e redisdb="0" 791778434480.dkr.ecr.us-east-1.amazonaws.com/openbridge/redisbackup' >> /ebs/logs/redisbackup/redisbackup.log 2>&1
```

The Cron export dynamically includes the timestamp in the filename. This allows you to easily run backups at set intervals, creating distinct point-in-time snapshots.


# Installation
## Docker Hub

The simplest way to get Redis Backup by pulling it from Docker Hub:
```bash
docker pull openbridge/redisbackup:latest
```

## AWS
Prerequisites: If you haven't installed the AWS CLI, please follow the instructions here to do so. If you haven't installed Docker, please follow the instructions here to do so.
1) Retrieve the docker login command that you can use to authenticate your Docker client to your registry:
```bash
aws ecr get-login --region us-east-1
```
2) Run the docker login command that was returned in the previous step.
3) Build your Docker image using the following command. For information on building a Docker file from scratch see the instructions here. You can skip this step if your image is already built:
```bash
docker build -t openbridge/redisbackup .
```
4) After the build completes, tag your image so you can push the image to this repository:
```bash
docker tag openbridge/redisbackup:latest yourlocation.ecr.us-east-1.amazonaws.com/openbridge/redisbackup:latest
```
5) Run the following command to PUSH this image to your newly created AWS repository:
```bash
docker push yourlocation.ecr.us-east-1.amazonaws.com/openbridge/redisbackup:latest
```

6) Run the following command to PULL this image to your newly created AWS repository:
```bash
docker pull yourlocation.ecr.us-east-1.amazonaws.com/openbridge/redisbackup:latest
```

Note: Make sure you replace the actual ECR URL with your own!

# Issues

If you have any problems with or questions about this image, please contact us through a GitHub issue.

# Contributing

You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

Before you start to code, we recommend discussing your plans through a GitHub issue, especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.

# Reference

You can read more about standard [Redis Persistence](https://redis.io/topics/persistence) to get a sense of your options with those formats.

# License
Copyright by Oleg Pudeyev (c) 2011-2016

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
