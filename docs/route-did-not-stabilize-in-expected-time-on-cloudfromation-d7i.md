# 云信息上的“路线未在预期时间内稳定”

> 原文：<https://dev.to/sot528/route-did-not-stabilize-in-expected-time-on-cloudfromation-d7i>

此错误是通过以下模板发生的:

```
...

  PNNatGateway:
    Type: 'AWS::EC2::NatGateway'
    Properties:
      AllocationId: eipalloc-xxxxxx
      SubnetId: !Ref PNPublicSubnet
  PNPrivateRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref PrivateNet
  PNPrivateRoute:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref PNPrivateRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref PNNatGateway

... 
```

Enter fullscreen mode Exit fullscreen mode

# 搞定

当我把`GatewayId`改成`NatGatewayId`
的时候，这个问题就解决了

```
...

  PNNatGateway:
    Type: 'AWS::EC2::NatGateway'
    Properties:
      AllocationId: eipalloc-xxxxxx
      SubnetId: !Ref PNPublicSubnet
  PNPrivateRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref PrivateNet
  PNPrivateRoute:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref PNPrivateRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref PNNatGateway

... 
```

Enter fullscreen mode Exit fullscreen mode

这是一个多么不友好的错误信息啊！