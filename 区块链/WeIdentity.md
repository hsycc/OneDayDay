<!--
 * @Author: hsycc
 * @Date: 2021-07-19 15:45:04
 * @LastEditTime: 2021-07-21 11:41:16
 * @Description:
 *
-->

# 部署 WeIdentity [https://weidentity.readthedocs.io/zh_CN/latest/index.html]

安装部署工具 [https://weidentity.readthedocs.io/zh_CN/latest/docs/weidentity-installation-by-web.html]

可视化部署 [https://weidentity.readthedocs.io/zh_CN/latest/docs/deploy-via-web.html]
联盟链管理员 命令行部署 [https://weidentity.readthedocs.io/zh_CN/latest/docs/weidentity-build-with-deploy.html]
非联盟链管理员 命令行部署 [https://weidentity.readthedocs.io/zh_CN/latest/docs/weidentity-build-without-deploy.html]

非联盟链管理员

插件
fisco-bcos-browser [https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/browser/index.html]

Java 应用中集成 WeIdentity Java SDK [https://weidentity.readthedocs.io/zh_CN/latest/docs/sdk-integration.html]
WeIdentity Java SDK [https://weidentity.readthedocs.io/projects/javasdk/zh_CN/latest/docs/weidentity-java-sdk-doc.html]

# WeIdentity-Sample 配置

```

# 节点证书配置文件
# ca.crt            gmca.crt     gmensdk.key  gmsdk.key   node.crt  weidentity.properties
# fisco.properties  gmensdk.crt  gmsdk.crt    log4j2.xml  node.key
cp -r /fisco/weid-build-tools/resources/*.crt /fisco/WeIdentity-Sample/resources
cp -r /fisco/weid-build-tools/resources/*.key /fisco/WeIdentity-Sample/resources
cp -r /fisco/weid-build-tools/resources/*.properties /fisco/WeIdentity-Sample/resources

# 密钥
cp /fisco/weid-build-tools/output/admin/ecdsa_key /fisco/WeIdentity-Sample/keys/priv

```

# 端口启用

6021 WeIdentity 安装部署工具 可视化 web 端口 建议在内网环境搭建安装部署。启动的可视化安装工具默认会监听 6021 端口，不能对外网开放访问。

#weid-build-tool
server.http.port=6020
server.port=6021

# run.config

```



#Note:Once you modify any of these configuration items, you need to re-run ./compile.sh.

#fill with your node ip and channel_listen_port, example: 0.0.0.0:20200
#if you have more than one node ,you can put them with separator","
#for example: 0.0.0.0:20200,0:0:0:1:20200
blockchain_address=127.0.0.1:20200

#this build tool supports FISCO BCOS 1.3.x and FISCO BCOS 2.0
#if you want to build on FISCO BCOS 1.3.X, please fill with "1",
#if you want to build on FISCO BCOS 2.0, please fill with "2".
#and default value is "2" with FISCO BCOS 2.0.
blockchain_fiscobcos_version=2

#your organization name, used for communication on blockchain.
org_id=DID

#amop_id
amop_id=baasze

#chain id
chain_id=1

#group id
group_id=1

#encrypt type, 0:非国密；1:国密
encrypt_type=1

#Configure your database information
persistence_type=mysql
mysql_address=39.103.184.180:3306
mysql_database=did
mysql_username=root
mysql_password=isV)od2jjg2x
redis_address=
redis_password=

#This variable is used to distinguish the environment. You can use "dev" to set the development environment,
#"stg" to set the test environment, "prd" to set the production environment.
#If you do not set it, the system will use allOrg as the environment by default.
#It is not recommended. Production use default configuration
cns_profile_active=prd


```
