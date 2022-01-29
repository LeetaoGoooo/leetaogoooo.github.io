---
title: Openlayers3仿链家网效果(二)
tags: [Javascript,Openlayers3]

date: 2017-06-02
published: true
hideInList: false
feature: 
isTop: false
---







![](http://ww1.sinaimg.cn/large/d9e82fa4ly1feucc4km7cg20bv0djhdt.gif)

接着上一篇的demo,经过几天的折腾，基本上完成了类似链家网的效果.上一篇文章的地址[Openlayers3仿链家网效果](http://www.leetao94.cn/2017/03/28/Openlayers%E4%BB%BF%E9%93%BE%E5%AE%B6%E7%BD%91%E5%8A%A0%E8%BD%BD%E6%95%88%E6%9E%9C/)

# 实现思路

## 不同 Zoom 加载不同的图层
通过 view 的 chaneg:resolution 事件，在不同 zoom 下加载不同的图层

```javascript
    var changeViewToZoom = function () {
        var view = map.getView();
        view.on('change:resolution', function (event) {
            var zoom = view.getZoom();
            if (zoom >= xxxZoom) {
                // 加载图层
            } else {
                // 加载其他图层
            }
        });
    };
```

## 圆圈的绘制

根据圆圈的中心点坐标和半径绘制圆

```javascript
    /**
     *  添加圆圈图层
     */
    function genarateCircleLayer(CircleObjArrs, title) {
        if (typeof arguments[2] != 'undefined') {
            return createCircleLayer(createCircleSource(CircleObjArrs, arguments[2]), title);
        }
        return createCircleLayer(createCircleSource(CircleObjArrs), title)
    }


    function createCircleLayer(source, title) {
        var circleLayer = new ol.layer.Vector({
            title: title,
            source: source,
            style: circleStyleFunction
        });
        return circleLayer;
    }


    function createCircleSource(CircleObjArrs) {
        var view = map.getView();
        var zoom = view.getZoom();
        var CircleSource = new ol.source.Vector();
        var radius = 1 / Math.pow(2, zoom - 6);
        // 判断是否定义 radius,否则才用默认值
        if (typeof (arguments[1]) != 'undefined') {
            radius = arguments[1];
        }
        for (var i = 0; i < CircleObjArrs.length; i++) {
            var feature = new ol.Feature({
                geometry: new ol.geom.Circle(CircleObjArrs[i].center, radius),
                text: CircleObjArrs[i].text
            });
            CircleSource.addFeature(feature);
        }

        return CircleSource;
    }
```

## 展示效果

展示效果包括:圆圈样式的切换,和圆圈下的图层显示.圆圈下的图层是在绘制圆圈的时候就一同加载了,只是样式设置透明，让它隐藏起来了,当鼠标移动到圆圈上，改变圆圈的样式的同时也改变了图层的样式，从而使得图层展示出来.主要调用**forEachFeatureAtPixel**方法，遍历当前坐标系下的所有feature,然后根据type切换不同样式

```javascript
    var displayFeature = function (pixel) {
        var features = [];
        map.forEachFeatureAtPixel(pixel, function (feature, layer) {
            if (layer.getVisible()) { // 当图层被隐藏之后,光标下该图层忽略
                features.push(feature);
            }
        });

        var len = features.length;
        if (len > 1) {
            for (var i = 0; i < len; i++) {
                var text = features[i].getProperties().text;
                var type = features[i].getGeometry().getType();
                features[i].setStyle(getSelectedPropertyStyle(type, text));
            }
        } else {
            var featureArr = map.getLayers().getArray();
            var featureArr_len = featureArr.length;
            for (var j = 0; j < featureArr_len; j++) {
                if (typeof featureArr[j].setStyle !== 'undefined') {
                    var source = featureArr[j].getSource();
                    features_by_source = source.getFeatures();

                    var feature_len = features_by_source.length;

                    for (var m = 0; m < feature_len; m++) {
                        if (typeof features_by_source[m].getProperties().text === 'undefined') {
                            text = 'none';
                        } else {
                            var text = features_by_source[m].getProperties().text
                        }
                        var type = features_by_source[m].getGeometry().getType();
                        features_by_source[m].setStyle(getUnselectedPropertyStyle(type, text));
                    }
                }
            }
        }
    };

    var getUnselectedPropertyStyle = function (type, text) {
        var style = null;
        if (type == 'Circle') {
            style = new ol.style.Style({
                fill: new ol.style.Fill({
                    color: 'rgba(57,172,106,0.9)',
                    opacity: '0.05'
                }),
                text: new ol.style.Text({
                    font: '14px sans-serif',
                    text: text,
                    textAlign: 'center',
                    rotateWithView: true,
                    fill: new ol.style.Fill({
                        color: '#FFFFFF'
                    }),
                    stroke: new ol.style.Stroke({
                        color: '#FFFFFF',
                        width: 1
                    })
                })
                // radius:100
            })
        }
        if (type == 'MultiPolygon' || type == 'Polygon') {
            style = new ol.style.Style({
                stroke: new ol.style.Stroke({
                    color: 'rgba(255, 255, 0, 0)',
                    width: 2
                }),
                fill: new ol.style.Fill({
                    color: 'rgba(255, 255, 0, 0)'
                })
            })
        }
        return style;
    };

    var getSelectedPropertyStyle = function (type, text) {
        var style = null;
        if (type == 'Circle') {
            style = new ol.style.Style({
                fill: new ol.style.Fill({
                    color: 'rgba(228,57,60,0.9)',
                    opacity: '0.05'
                }),
                text: new ol.style.Text({
                    font: '14px sans-serif',
                    text: text,
                    textAlign: 'center',
                    // rotateWithView:true,
                    fill: new ol.style.Fill({
                        color: '#FFFFFF'
                    }),
                    stroke: new ol.style.Stroke({
                        color: '#FFFFFF',
                        width: 1
                    })
                })
            })
        }
        if (type == 'MultiPolygon' || type == 'Polygon') {
            style = new ol.style.Style({
                stroke: new ol.style.Stroke({
                    color: 'green',
                    width: 2
                }),
                fill: new ol.style.Fill({
                    color: 'rgba(57,172,106,0.1)'
                })
            })
        }
        return style;
    };
```
