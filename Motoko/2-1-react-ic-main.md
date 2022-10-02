# 快速实现 React 或 Vue 代码部署到 IC 主网

## 如何部署前端代码到 IC 主网

1.配置 dfx 文件

`canister_ids.json`

```
{
  "assets": {
    "ic": "cmiki-zqaaa-aaaag-aabca-cai"
  },
  "microService": {
    "ic": "drsko-yiaaa-aaaal-qadxa-cai"
  },
}
```

2.添加准备的 canister id

<https://nns.ic0.app>

cmiki-zqaaa-aaaag-aabca-cai

drsko-yiaaa-aaaal-qadxa-cai

3.启动和打包本地测试

dfx start

dfx deploy

4.发布主网

sudo dfx canister --network ic uninstall-code --all

sudo dfx build --network ic

sudo dfx deploy --network ic

sudo dfx canister --network ic install --all

5.如何使用 xtc 充值 cycle

XTC 充值操作:

消耗 XTC 充值 cycle，12 位的精度

`dfx canister --network=ic call drsko-yiaaa-aaaal-qadxa-cai burn "(record { canister_id= principal \"cmiki-zqaaa-aaaag-aabca-cai\"; amount= (20000000000000:nat64)})"`

查看 XTC 的数量

`dfx canister --network=ic call --query drsko-yiaaa-aaaal-qadxa-cai balanceOf "(principal \"exp33-minxe-lqmzo-dh3fa-ostfz-tkaue-kn7ow-6cioh-gzfw7-px7yn-pqe\")"`

使用 XTC 直接创建 canister，并设置 controller

`dfx canister --network=ic call drsko-yiaaa-aaaal-qadxa-cai wallet_create_canister "(record {cycles= (3900000000000:nat64); controller= (principal \"$(dfx identity get-principal)\"); })"`
