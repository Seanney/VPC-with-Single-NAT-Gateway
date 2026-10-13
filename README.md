# VPC-with-Single-NAT-Gateway
Designed and configured VPC in AWS with Single NAT Gateway
# Cost-Optimized AWS VPC with Single NAT Gateway

A multi-AZ VPC architecture in `eu-north-1` with public and private subnets, using a single NAT Gateway for outbound internet access from private subnets. Designed to minimize fixed costs while still providing basic multi-AZ subnet distribution.

## Architecture Overview

Component              Count Details                                      
VPC                    1     `10.0.0.0/16`                                
Public Subnets         2     One in each AZ (`eu-north-1a`, `eu-north-1b`) 
Private Subnets        2     One in each AZ                               
Internet Gateway       1     Attached to VPC                              
NAT Gateways           1     Single NAT in `eu-north-1a`                  
Elastic IPs            1     Attached to the NAT Gateway                  
Route Tables           2     1 public + 1 private (shared)                

### Network Layout

Subnet              CIDR          AZ            Type    Purpose                          
public_subnet1      10.0.1.0/24   eu-north-1a   Public  NAT Gateway, public resources    
public_subnet2      10.0.2.0/24   eu-north-1b   Public  Public resources                 
private_subnet1     10.0.3.0/24   eu-north-1a   Private Application tier (AZ-a)          
private_subnet2     10.0.4.0/24   eu-north-1b   Private Application tier (AZ-b)          

## Design Decisions

Decision                    Choice                          Reason                                      
Number of AZs               2                               Subnet distribution across AZs              
NAT Gateway strategy        Single NAT                      Cost optimization (~50% lower fixed cost)   
Public route table          Shared                          Both public subnets route to IGW            
Private route table         Shared                          Both private subnets route to the same NAT  
map_public_ip_on_launch     Enabled on public subnets       Automatic public IPs                        

## Traffic Flow

1. **Public subnet resources** → Internet Gateway → Internet
2. **Private subnets (both AZs)** → Single NAT Gateway (in AZ-a) → Internet Gateway → Internet

### Trade-offs of This Design

Aspect                  Impact                                      
Cost                    Lower (~$33/month fixed vs ~$66 for dual NAT) 
High Availability       Lower — NAT is a single point of failure    
Cross-AZ traffic        Yes — private subnet in AZ-b crosses AZ     
Best for                Dev/test, non-critical workloads            

## Cost Implications

Resource              Approximate Monthly Cost Notes                          
NAT Gateway (x1)      ~$33                     $0.045/hour                    
Data processing       Variable                 $0.045 per GB                  
Elastic IP            $0                       Free when associated           
**Fixed baseline**    **~$33/month**           Before data transfer           


## How to Deploy
terraform init
terraform plan
terraform apply
