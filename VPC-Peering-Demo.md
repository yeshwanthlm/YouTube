## VPC Peering Demo - CloudFormation Template

```bash
Description:  VPC Peering Demo
Parameters:
  LatestAmiId:
    Description: AMI for Bastion Host (default is latest AmaLinux2)
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2'
Resources:
  VPCA:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.16.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: vpc-a
  VPCB:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.17.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: vpc-b
  
  SubnetA:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPCA
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.16.0.0/20
      Tags:
        - Key: Name
          Value: SubnetA
  SubnetB:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPCB
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.17.0.0/20
      Tags:
        - Key: Name
          Value: SubnetB
  
  VPCASecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      VpcId: !Ref VPCA
      GroupDescription: Enable SSH access via port 22 IPv4 & v6
      SecurityGroupIngress: 
        - Description: 'Allow SSH IPv4 IN'
          IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: '0.0.0.0/0'
        - Description: 'Allow SSH IPv6 IN'
          IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIpv6: ::/0
  VPCBSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      VpcId: !Ref VPCB
      GroupDescription: Enable SSH access via port 22 IPv4 & v6
      SecurityGroupIngress: 
        - Description: 'Allow SSH IPv4 IN'
          IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: '0.0.0.0/0'
        - Description: 'Allow SSH IPv6 IN'
          IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIpv6: ::/0 
  VPCASecurityGroupSelfReferenceRule:
    Type: "AWS::EC2::SecurityGroupIngress"
    Properties:
      GroupId: !Ref VPCASecurityGroup
      IpProtocol: '-1'
      SourceSecurityGroupId: !Ref VPCASecurityGroup
  VPCBSecurityGroupSelfReferenceRule:
    Type: "AWS::EC2::SecurityGroupIngress"
    Properties:
      GroupId: !Ref VPCBSecurityGroup
      IpProtocol: '-1'
      SourceSecurityGroupId: !Ref VPCBSecurityGroup
  PrivateEC2A:
    Type: AWS::EC2::Instance
    DependsOn: 
      - ssminterfaceendpoint
      - ssmec2messagesinterfaceendpoint
      - ssmmessagesinterfaceendpoint
    Properties:
      InstanceType: "t2.micro"
      ImageId: !Ref LatestAmiId
      IamInstanceProfile: !Ref EC2InstanceProfile
      SubnetId: !Ref SubnetA
      SecurityGroupIds: 
        - !Ref VPCASecurityGroup
      Tags:
        - Key: Name
          Value: EC2-VPC-A
  PrivateEC2B:
    Type: AWS::EC2::Instance
    DependsOn: 
      - ssminterfaceendpoint
      - ssmec2messagesinterfaceendpoint
      - ssmmessagesinterfaceendpoint
    Properties:
      InstanceType: "t2.micro"
      ImageId: !Ref LatestAmiId
      IamInstanceProfile: !Ref EC2InstanceProfile
      SubnetId: !Ref SubnetB
      SecurityGroupIds: 
        - !Ref VPCBSecurityGroup
      Tags:
        - Key: Name
          Value: EC2-VPC-B  
  EC2Role:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service:
              - ec2.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      Policies:
        - PolicyName: root
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action: 
                  - 'ssm:DescribeAssociation'
                  - 'ssm:GetDeployablePatchSnapshotForInstance'
                  - 'ssm:GetDocument'
                  - 'ssm:DescribeDocument'
                  - 'ssm:GetManifest'
                  - 'ssm:GetParameter'
                  - 'ssm:GetParameters'
                  - 'ssm:ListAssociations'
                  - 'ssm:ListInstanceAssociations'
                  - 'ssm:PutInventory'
                  - 'ssm:PutComplianceItems'
                  - 'ssm:PutConfigurePackageResult'
                  - 'ssm:UpdateAssociationStatus'
                  - 'ssm:UpdateInstanceAssociationStatus'
                  - 'ssm:UpdateInstanceInformation'
                Resource: '*'
              - Effect: Allow
                Action:
                  - 'ssmmessages:CreateControlChannel'
                  - 'ssmmessages:CreateDataChannel'
                  - 'ssmmessages:OpenControlChannel'
                  - 'ssmmessages:OpenDataChannel' 
                Resource: '*'
              - Effect: Allow
                Action: 
                  - 'ec2messages:AcknowledgeMessage'
                  - 'ec2messages:DeleteMessage'
                  - 'ec2messages:FailMessage'
                  - 'ec2messages:GetEndpoint'
                  - 'ec2messages:GetMessages'
                  - 'ec2messages:SendReply'
                Resource: '*'
              - Effect: Allow
                Action:
                  - 's3:*'
                Resource: '*'
              - Effect: Allow
                Action:
                  - 'sns:*'
                Resource: '*'
  EC2InstanceProfile:
    Type: 'AWS::IAM::InstanceProfile'
    Properties:
      Path: /
      Roles:
        - !Ref EC2Role
  ssminterfaceendpoint:
    Type: AWS::EC2::VPCEndpoint
    Properties:
      VpcEndpointType: "Interface"
      PrivateDnsEnabled: "True"
      SubnetIds:
        - !Ref SubnetA
      SecurityGroupIds:
        - !Ref VPCASecurityGroup
      ServiceName: !Sub com.amazonaws.${AWS::Region}.ssm
      VpcId: !Ref VPCA
  ssmec2messagesinterfaceendpoint:
    Type: AWS::EC2::VPCEndpoint
    Properties:
      VpcEndpointType: "Interface"
      PrivateDnsEnabled: "True"
      SubnetIds:
        - !Ref SubnetA
      SecurityGroupIds:
        - !Ref VPCASecurityGroup
      ServiceName: !Sub com.amazonaws.${AWS::Region}.ec2messages
      VpcId: !Ref VPCA
  ssmmessagesinterfaceendpoint:
    Type: AWS::EC2::VPCEndpoint
    Properties:
      VpcEndpointType: "Interface"
      PrivateDnsEnabled: "True"
      SubnetIds:
        - !Ref SubnetA
      SecurityGroupIds:
        - !Ref VPCASecurityGroup
      ServiceName: !Sub com.amazonaws.${AWS::Region}.ssmmessages
      VpcId: !Ref VPCA
      
 ```
