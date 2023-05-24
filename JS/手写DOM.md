# 手写DOM

## 增

### 直接创建节点

```js
window.dom = {
    create(tagName){
        return document.createElement(tagName)
    }
}

const div = dom.create('div')
```

但是上面的代码存在的问题是，如果我想在`div`内再包裹其他元素，这个代码无法实现，所以进行改写：

```js
window.dom = {
    create(string){
        const container = document.createElement('div')
        container.innerHTML = string
        return container.children[0]
    }
}

const div = dom.create('<div><span>1</span></div>')
// 可以去试一下哦
```

但是，有bug，因为如果`div`容器内放了`td`不符合语法，所以需要把`div`换成`template`

但是`template`里面的元素不能通过`children`来拿到

```js
window.dom = {
    create(string){
        const container = document.createElement('template')
        container.innerHTML = string.trim()
        // 要trim一下，防止出现空格，导致firstChild为文本
        return container.content.firstChild
    }
}
const div = dom.create('<tr><td>1</td></tr>')
```

### 把一个节点创建在另一个节点之后

```js
window.dom = {
    after(node,node2){
        node.parentNode.insertBefore(node2,node.nextSibling)
    }
}
// 把node2插入到node的节点之后
// 思路:相当于把node2插入到node下个节点的前面
```

### 把一个节点插入到另一个节点之前

```js
window.dom = {
    before(node,node2){
        node.parentNode.insertBefore(node2,node)
    }
}
```

### 新增儿子

```
window.dom = {
    append(parent,node){
        parent.appendChild(node)
    }
}
```

### 新增爸爸

```js
window.dom = {
   wrap(node,parent){
        dom.before(node,parent)
        dom.append(parent,node)
    }
}
// 假设div1是div2的爸爸，现在要在div1和div2中插入div3
// div3先和div2做兄弟（把div3插入到div2之前）
// 然后把div2变成div3的儿子
```

## 删

### 直接删除节点

```js
window.dom = {
   remove(node){
        node.parentNode.removeChild(node)
        return node
    }
}
// 找到删除节点的父节点，然后让父节点删除
// 同时还能得到删除节点
```

### 清空一个节点的所有子节点

```js
window.dom = {
   empty(node){
        //const {childNodes} = node.childNodes
        //const array = []
        //for(let i = 0;i<childNodes.length;i++){
            //dom.remove(childNodes[i])
            //array.push(childNodes[i])
        //}
        //return array
       // 上面有bug，因为for循环删除时，childNodes,length会变
       const {childNodes} = node.childNodes
        const array = []
        let {firstChild} = node
        while ((firstChild)){
            array.push(dom.remove(node.firstChild))
            firstChild = node.firstChild
        }
        return array
    }
}
```

## 改

### 读写属性

```js
window.dom = {
   attr(node, name, value) {
        if (arguments.length === 3) {
            node.attributes(name, value)
        }else if (arguments.length ===2){
            return node.getAttribute(name)
        }
    },
}
// 长度为3，写入内容；长度为2，读内容
```

### 读写文本内容

```js
window.dom = {
  	text(node,string){
     	if(arguments.length ===2){
            node.innerText = string
        }else if (arguments.length ===1){
            return node.innerText
        }
   	},
}
```

### 读写html

```js
window.dom = {
  	html(node,string){
        if(arguments.length ===2){
            node.innerHTML = string
        }else if (arguments.length ===1){
            return node.innerHTML
        }
    }
}
```

### 读写style

```js
window.dom = {
  	style(node,name,value){
        if(arguments.length ===3 ){
            //dom.style(div,'color','red')
            node.style[name] = value
        }else if (arguments.length===2){
            if(typeof  name === "string"){
                //dom.style(div,'color')
                return node.style[name]
            }else if(name instanceof Object){
                //dom.style(div,{color:'red'})
                for(let key in name){
                    node.style[key] = name[key]
                }
            }
        }
    },
}
```

### 增加、修改、删除class

```js
window.dom = {
  	 class:{
        add(node,className){
            node.classList.add(className)
        },
        remove(node,className){
            node.classList.remove(className)
        },
        has(node,className){
            return node.classList.contains(className)
        }
    },
}
```

### 增加、删除事件监听

```js
window.dom = {
  	on(node,eventName,fn){
        node.addEventListener(eventName,fn)
    },
    off(node,eventName,fn){
        node.removeEventListener(eventName,fn)
    },
}
```

## 查

### 获取标签(们)

```
window.dom = {
  	find(selector,scope){
        return (scope || document).querySelectorAll(selector)
    },
}
```

### 获取父节点

```js
window.dom = {
  	parent(node){
        return node.parentNode
    },
}
```

### 获取子节点

```js
window.dom = {
  	children(node){
        return node.children
    }
}
```

### 获取兄弟节点

```js
window.dom = {
  	siblings(node){
        return Array.from(node.parentNode.children).filter(n=>n!=node)
    }
}
```

### 获取下一个节点

```js
window.dom = {
  	next(node){
        let x = node.nextSibling
        while (x && x.nodeType === 3){
            x = x.nextSibling
        }
        return x
    }
}
```

### 获取上一个节点

```js
window.dom = {
  	previous(node){
        let x = node.previousSibling
        while (x && x.nodeType === 3){
            x = x.previousSibling
        }
        return x
    }
}
```

