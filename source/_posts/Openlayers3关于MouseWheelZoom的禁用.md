---
title: Openlayers3关于MouseWheelZoom的禁用
tags: [Javascript,Openlayers3]

date: 2017-06-02
published: true
hideInList: false
feature: 
isTop: false
---







由于使用鼠标滚轮控制地图缩放不可控,所以决定禁用鼠标滚轮事件.代码如下:

```javascript
map.getInteractions().forEach(function (interaction) {
    if (interaction instanceof ol.interaction.MouseWheelZoom) {
        map.removeInteraction(interaction);
    }
});
```