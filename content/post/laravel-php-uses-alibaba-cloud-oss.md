---
title: "laravel php 使用阿里云OSS"
date: 2019-01-24T00:44:27+08:00
typora-root-url: ..\..\static
tags: ["laravel", "php", "阿里云OSS"]
categories: ["PHP", "Laravel"]
draft: false
---

前段时间做了一个视频上传,及存储播放的功能,使用的是阿里云的OSS,总结记录下

首先当然是开通阿里云的OSS对象存储,然后新建一个 Bucket

![create bucket](/images/laravel-php-uses-alibaba-cloud-oss.assets/FEZRBE@K4ZEMU1HPUKT.png)

新建 Bucket

然后就是新建一个 AccessKey,下面进入正题

我采取的方式是最佳实践中的web端直传,这样可以不耗费本身服务器资源,但这里面有几点是需要注意的,上传视频时的服务器签名和授权访问

先来看一下上传视频时的服务器签名,文档的具体位置在
这 [https://help.aliyun.com/document_detail/31926.html?spm=a2c4g.11174283.6.1123.5c677da2zyxDEi](https://help.aliyun.com/document_detail/31926.html?spm=a2c4g.11174283.6.1123.5c677da2zyxDEi),如果在前端直接使用 OSS AccessKey 的话,会有很高的安全隐患,因为web直传的解决方案并没有太多涉及到OSS,所以我没有引入官方的SDK,而是手动实现, 下面看后端生成签名代码

```php
public function ali_oss_verity()
{
	$dir = 'video/course/';//存放文件基础目录
	$end = time() + config('site.ali_oos.expire');//过期时间

	$policy = [
		'expiration' => $this->gmt_iso8601($end),
		'conditions' => [
			[0=>'content-length-range', 1=>0, 2=>config('site.ali_oos.max_size')],//最大文件大小.用户可以自己设置
			[0=>'starts-with', 1=>'$key', 2=>$dir]// 表示用户上传的数据，必须是以$dir开始，不然上传会失败，这一步不是必须项，只是为了安全起见，防止用户通过policy上传到别人的目录。
		]
	];

	$base64_policy = base64_encode(json_encode($policy));

	$signature = base64_encode(hash_hmac('sha1', $base64_policy, config('site.ali_oos.AccessKeySecret'), true));

	return [
		'accessid' => config('site.ali_oos.AccessKeyId'),
		'host' => config('site.ali_oos.host'),//格式为 bucketname.endpoint
		'policy' => $base64_policy,
		'signature' => $signature,
		'expire' => $end,
		'dir' => $dir,
	];
}

public function gmt_iso8601($time) {
	$dtStr = date("c", $time);
	$mydatetime = new \DateTime($dtStr);
	$expiration = $mydatetime->format(\DateTime::ISO8601);
	$pos = strpos($expiration, '+');
	$expiration = substr($expiration, 0, $pos);
	return $expiration."Z";
}
```

逻辑比较简单,通过一些参数生成签名,前端的代码就更简单了,具体的案例在这[http://oss-demo.aliyuncs.com/oss-h5-upload-js-php/index.html?spm=a2c4g.11186623.2.13.4a964382ResGwv](http://oss-demo.aliyuncs.com/oss-h5-upload-js-php/index.html?spm=a2c4g.11186623.2.13.4a964382ResGwv)

下面说的就是文件的授权访问了,访问是需要授权的,这样可以防止流量大量丢失,核心代码如下

```php
public function getVideoUrlAttribute($video_url)
{
	$time = time()+7200;//过期时间.这里设施的是两个小时
	$string_to_sign_ordered = "GET\n\n\n".$time."\n/".config('site.ali_oos.bucket').'/'.$video_url;
	$signature = base64_encode(hash_hmac('sha1', $string_to_sign_ordered, config('site.ali_oos.AccessKeySecret'), true));
	return config('site.ali_oos.host').'/'.$video_url.'?OSSAccessKeyId='.rawurlencode(config('site.ali_oos.AccessKeyId')).'&Expires='.$time.'&Signature='.rawurlencode($signature).'';
}
```

这里使用了laravel中Eloquent的修改器,因为没有引入SDK,导致这里的算法签名需要自己手动实现,折腾了一段时间,不过好在折腾出来了,具体的生成签名参考官方文档[https://www.alibabacloud.com/help/zh/doc-detail/31951.htm?spm=a2c63.p38356.b99.436.60d49209aJ7jFl.](https://www.alibabacloud.com/help/zh/doc-detail/31951.htm?spm=a2c63.p38356.b99.436.60d49209aJ7jFl.)
