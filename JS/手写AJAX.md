# 手写AJAX

常用的库：`Axios`,`jquery`,`VueResource`或者`windwo.fetch`

```js
let xhr = new XMLHttpRequest()

xhr.open('GET','/xxx')
// 低级写法
xhr.onload = ()=>{
    console.log('得到内容')
}
xhr.onerror = ()=>{
    console.log('失败了')
}
// 高级写法
xhr.onreadystatechange = function(){
    if(xhr.readyState === 4){ // 0~4有不同含义
// 0:创建了但是还没open 1:open已被调用 2:send已被调用 3:下载中 4:下载操作完成
        if(xhr.status>=200 && xhr.status<300 || xhr.status ===304){
            success(xhr)
        }else{
            fail(xhr)
        }
    }
}
xhr.send('{"name":"hello"}')


```

