
---
title: PHP中用json_decode()无法解析有换行符的字符串
tags: [未分类]
categories: [Javascript]
toc: false
date: 2016-09-30 16:48:39
---

$temp_mes_data = array (
  'state' => 'on',
  'temp_mess_id' => '2IWKf1L7pbfFcqCBfzIB-P9coclpprUwwY2PkEPot1g',
  'temp_content' =>
  array (
    'first' =>
    array (
      'value' => '您刚刚收到了一笔{pay_fee}元的新订单',
    ),
    'keyword1' =>
    array (
      'value' => '{unique_id}',
    ),
    'keyword2' =>
    array (
      'value' => '{info}',
    ),
    'keyword3' =>
    array (
      'value' => '{member}',
    ),
    'keyword4' =>
    array (
      'value' => '{mobile}',
    ),
    'keyword5' =>
    array (
      'value' => '{address}',
    ),
    'remark' =>
    array (
      'value' => '点击这里查看订单详情',
    ),
  ),
  'url' => 'http://shop.ci123.com/yiqigou/store_entity/orderDetail/{trade_id}?store_id={store_id}',
);
$data = array (
  'store_id' => '302',
  'trade_id' => '203245',
  'pay_fee' => '0.01',
  'unique_id' => '1609301545079469',
  'info' => '
贝拉小蜜蜂 5-12岁儿童牙膏防蛀健齿牙牙疼宝宝牙膏  x1',
  'member' => '<E6>',
  'mobile' => '',
  'address' => '',
);

$temp_mes_data = json_encode($temp_mes_data);
foreach ($data as $k => $v) {
    $temp_mes_data = str_replace("{$k}", $v, $temp_mes_data);
}
$temp_mes_data = json_decode($temp_mes_data);
var_dump($temp_mes_data);
```

在此输入正文




