---
title: React中使用事件总线
date: 2020-10-19 16:14:08
tags: React
---
```

class EventBus {
    constructor() {
        this.events = this.events || new Object()
    }

    /**
     * 触发函数
     * @param {*} type  事件类型
     * @param  {...any} args 要传递的参数
     */
    emit(type, ...args) {
        let event = this.events[type]
        if (Array.isArray(event)) {
            for (let i = 0; i < event.length; i++) {  
                event[i].apply(this, args)
            }
        } else {
            event[0].apply(this, args)
        }
    }

    /**
     * 监听函数
     * @param {*} type  监听的事件类型
     * @param {*} fun   出发时执行的回调
     */
    on(type, fun) {
        let event = this.events[type]
        if (!event) {
            this.events[type] = [fun]
        } else {
            event.push(fun)
        }
    }
    /**
     * 销毁事件
     * @param {*} eventName  事件名称
     */
    removeEvent(eventName) {

        eventName === undefined ? this.event = {}: this.event.delete[eventName]
        
    }
}

let eventBus = new EventBus()
export default eventBus

```
