---
title: "element-ui 表单验证rule不识别"
date: 2018-11-14T00:36:01+08:00
typora-root-url: ..\..\static
tags: ["element-ui", "表单验证"]
categories: ["Element-UI"]
draft: false
---

 最近在使用laravel+vue+element-ui编写程序,在使用表单验证时,出现表单有值但是验证不通过的情况,后来恍然大悟,是要v-model的字段和prop字段一样才行,来看下具体栗子:

```vue
<el-form-item :label="$t('table.menus.meta_title')" prop="title">
    <el-input v-model="temp.title" placeholder="title"/>
</el-form-item>
```

```js
title: [{required: true, message: '标签名称必填', trigger: 'blur'}],
```

特别注意的是prop的字段,开始是写的meta_title字段,一直不识别,后来想了一下,是需要和v-model的字段一样才行