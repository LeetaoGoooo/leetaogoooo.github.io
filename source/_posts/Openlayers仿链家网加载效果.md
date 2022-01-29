---
title: Openlayers仿链家网加载效果
tags: [Javascript,Openlayers3]

date: 2017-03-28
published: true
hideInList: false
feature: 
isTop: false
---







# 效果预览

在上代码之前首先让我们看一下链家网的加载效果，也就是这次我们的模仿对象，效果如下图:

![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fe2dmqh07hg20qk0gaqv6.gif)

下面是我们实现的效果:

![](http://ww1.sinaimg.cn/large/d9e82fa4ly1fe2dq2tou6g20qk0gaqv7.gif)

# 实现思路

当 *Zoom* 改变的时候，根据圆的**Radiu** 和 **Zoom**，计算出适合当前地图的圆的半径，然后重新绘圆

# 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Gis展示</title>
    <link rel="stylesheet" href="jslib/openlayer3/css/ol.css" type="text/css">
    <link rel="stylesheet" href="jslib/jeasyui/themes/default/easyui.css" type="text/css">
    <script src="jslib/openlayer3/build/ol-debug.js"></script>
    <script src="jslib/jquery.min.js"></script>
    <script src="jslib/jeasyui/jquery.easyui.min.js"></script>
</head>
<body>
    <div id="mapContainer" class="subcontent">    
        <div id="map" class="map" style="width: auto;height: auto"></div>
    </div>
    <script>
      var styles = {
        'Circle': new ol.style.Style({
          fill: new ol.style.Fill({
            color: 'rgba(57,172,106,0.9)',
            opacity: '0.05'
          }),
          text: new ol.style.Text({
            font: '14px sans-serif',
            text:'Test\n\n Value \n\n Message',
            textAlign:'center',
            rotateWithView:true,
            fill: new ol.style.Fill({
                color: '#FFFFFF'
            }),
            stroke:new ol.style.Stroke({
                color:'#FFFFFF',
                width: 1
            })
          })
          // radius:100
        })
      };
      var Selectstyles = {
        'Circle': new ol.style.Style({
          fill: new ol.style.Fill({
            color: 'rgba(228,57,60,0.9)',
            opacity: '0.05'
          }),
          text: new ol.style.Text({
            font: '14px sans-serif',
            text:'Test\n\n Value \n\n Message',
            textAlign:'center',
            // rotateWithView:true,
            fill: new ol.style.Fill({
                color: '#FFFFFF'
            }),
            stroke:new ol.style.Stroke({
                color:'#FFFFFF',
                width: 1
            })
          })
        })
      };
      var styleFunction = function(feature) {
        return styles[feature.getGeometry().getType()];
      };
      var selectStyleFunction = function(feature) {
        return Selectstyles[feature.getGeometry().getType()];
      }
      var vectorSource = new ol.source.Vector({
        features: new ol.Feature(new ol.geom.Circle([5e6, 7e6], 1e6))
      });
      vectorSource.addFeature(new ol.Feature(new ol.geom.Circle([5e6, 7e6], 1e6/Math.pow(2,-1))));
      var vectorLayer = new ol.layer.Vector({
        source: vectorSource,
        style: styleFunction
      });
      var map = new ol.Map({
        layers: [
          new ol.layer.Tile({
            source: new ol.source.OSM()
          }),
          vectorLayer
        ],
        target: 'map',
        controls: ol.control.defaults({
          attributionOptions: /** @type {olx.control.AttributionOptions} */ ({
            collapsible: false
          })
        }),
        view: new ol.View({
          center: [0, 0],
          zoom: 2
        })
      });
      var view = map.getView();
      view.on('change:resolution',function(event){
            var zoom = view.getZoom();
            var Collection = map.getLayers();
            var layers = Collection.getArray();
            var vectorSource = layers[1].getSource();
            var features = vectorSource.getFeatures();
            var len = features.length;
            for(var i = 0 ; i< len; i++) {
                var Circle = features[i].getGeometry();
                console.log(zoom);
                Circle.setRadius(1e6/Math.pow(2,zoom-3));
            }
      });
      var selectPointerMove = new ol.interaction.Select({
        condition: ol.events.condition.pointerMove
      });
      map.addInteraction(selectPointerMove);
      selectPointerMove.on('select',function(e) {
         var ele = e;
         var select_len = e.selected.length;
         var unselect_len = e.deselected.length;
         if (select_len > 0) {
            var featureArr = e.selected;
            for (var i = 0; i < featureArr.length; i++) {
              featureArr[i].setStyle(selectStyleFunction(featureArr[i]));
                var Circle = featureArr[i].getGeometry();
            }
         }
         if (unselect_len > 0) {
            var featureArr = e.deselected;
            for (var i = 0; i < featureArr.length; i++) {
                featureArr[i].setStyle(styleFunction(featureArr[i]));
                var Circle = featureArr[i].getGeometry();
            }
         }
      })
    </script>
</body>
</html>
```
