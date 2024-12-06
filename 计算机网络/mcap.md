# MCAP

## MCAP简介

MCAP是一个开源的协议，用来统一结构化和非结构化的数据，结束工业上各种机器人通信的协议冗杂的局面

## MCAP组成

> MCAP主要有三个概念：message, channel 和 schema

### Message

节点间pub/sub的通信单元

### Channel

具有相同类型或者说是相同schema的数据流。一般对应一个publisher和subscriber之间的连接

### Schema

用来描述Channel中的数据格式，可能是Protocol Schema或者是Json Schema
