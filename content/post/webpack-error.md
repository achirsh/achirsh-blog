---
title: "webpack错误"
date: 2021-09-13
draft: false
summary: "webpack错误"
---

# webpack错误

## 1、WARNING in asset size limit: The following asset(s) exceed the recommended size limit (244 KiB)

方式1：

    performance: {
        hints: false
    }  

方式2：

    performance: {
        hints: 'warning',
        maxAssetSize: 30000000,
        maxEntrypointSize: 50000000,
        assetFilter: function(assetFilename) {
            return assetFilename.endsWith('.css') || assetFilename.endsWith('.js')
        }
    }
