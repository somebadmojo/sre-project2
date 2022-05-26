# Infrastructure

## AWS Zones
Identify your zones here

| Region | Availability zones | Notes |
|--------|--------------------|-------|
| us-east-2 | us-east-2a, us-east-2b, us-east-2c | Primary region |
| us-west-1 | us-west-1a, us-west-1b | Secondary Region |


aws ec2 describe-availability-zones --region us-west-1
{
    "AvailabilityZones": [
        {
            "RegionName": "us-west-1",
            "ZoneName": "us-west-1a",
        },
        {
            "RegionName": "us-west-1",
            "ZoneName": "us-west-1b",
        }
    ]
}

aws ec2 describe-availability-zones --region us-east-2
{
    "AvailabilityZones": [
        {
            "RegionName": "us-east-2",
            "ZoneName": "us-east-2a",
        },
        {
            "RegionName": "us-east-2",
            "ZoneName": "us-east-2b",
        },
        {
            "RegionName": "us-east-2",
            "ZoneName": "us-east-2c",
        }
    ]
}



## Servers and Clusters

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Asset name | Brief description | AWS size eg. t3.micro (if applicable, not all assets will have a size) | Number of nodes/replicas or just how many of a particular asset | Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere |
| EC2 Instance | Web server | t3.micro | 6 | 3 deployed to pri region, 3 deployed to sec region |
| EKS cluster | Observability with Prometheus Grafana | t3.medium | 4 | 2 clusters of 2 nodes each, deployed to pri and sec regions. |
| RDS cluster | Database | db.t2.small | 4 | cluster in pri region replicated to sec region, 5 day backup retention | 
| S3 bucket | Data store for terraform code | na | 2 | one bucket each in pri and sec regions |
| Application Load Balancer (ALB) | Load Balancing for the web front end | 2 | 1 ALB per region servicing all the AZs in the region.
| Multi-AZ VPCs | Virtual Private Cloud, distributed flat networks across mult AZs. | 2 | 1 VPC per Region providing network services to all AZs.

### Descriptions
Assets to be deployed to primary and secondary regions.

2x S3 buckets. One in each region.

3x ubuntu EC2 instances for web in each region.

2x nodes per kubernetes cluster in each region, prometheus/grafana installed.

VPCs have IPs in multiple availability zones.

Application load balancer deployed in each region for the web servers.

2x RDS clusters, one in each region. 

Each RDS cluster will have primary/secondary nodes deployed to same region, but separate AZs.

5 day retention for RDS backups.

RDS Region 1 will replicate to Region 2


## DR Plan
### Pre-Steps:
1) A full infrastructure stack will be deployed to both the primary and secondary regions.  Configuration should be near identical as they're designed to service identical workload.
2) There needs to be continuous audit that the secondary location is functional and configured identically to the primary location.  This can be performed using any tool like ansible, chef, puppet, terraform.  Any config drift should be flagged and remediated immediately upon detection.
3) Create service monitors and synthetics to continuously monitor functionality of the secondary infrastructure.  Otherwise you're gambling on failing over to a possibly broken site.

## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region

1) Promote RDS cluster at the secondary location to primary.  
2) Validate DB health with the web/app servers in the secondary location.
3) Fail over or redeploy any apps that need to run on EKS in the secondary location (prometheus/grafana)
4) Fail over DNS to point at the ALB at the secondary location. This will move web/app traffic over to the secondary location. Health should now be visible in grafana.
