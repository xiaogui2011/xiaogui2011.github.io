---
title: 微信群成员统计
date: 2024-05-14 10:49:49 +0800
author: john
categories: [code, js]
tags: oneRiver wechat
layout: post
---

## 微信网页登录
[wechat-need-web](https://github.com/lqzhgood/wechat-need-web?tab=readme-ov-file#wechat-need-web)


## 浏览器执行脚本

```js
let membersDiv = document.querySelector('.members_inner');
let images = membersDiv.getElementsByClassName('avatar');

async function observeMutation(targetNode) {
    const config = { childList: true, subtree: true };
    return new Promise(resolve => {
        const observer = new MutationObserver((mutationsList) => {
            for(let mutation of mutationsList) {
                if (mutation.type === 'childList') {
                    let mmpop_profile = document.querySelector('#mmpop_profile');
                    if (mmpop_profile) {
                        let nicknameElement = mmpop_profile.querySelector('.nickname.ng-binding');
                        if (nicknameElement) {
                            resolve(nicknameElement.innerText);
                            observer.disconnect();
                        }
                    }
                }
            }
        });
        observer.observe(targetNode, config);
    });
}

async function performClicks(images) {
    for(let i = 0; i < images.length; i++) {
        let beforeChanges = images[i].nextElementSibling.innerText;
        images[i].click();
        let afterChanges = await observeMutation(document.body);
        console.error(beforeChanges + ',' + afterChanges);
        await new Promise(r => setTimeout(r, 10));
    }
}

performClicks(images);
```


保存log到`/tmp/xxx.log`文件

```python
filename = '/tmp/xxx.log'
with open(filename, 'r') as file:
    lines = [line for line in file if ',' in line]
    
with open('/tmp/new.csv', 'w') as output_file:
    output_file.writelines(lines)
```

**最后使用VIM进行文本处理成csv即可**
