# 关于问题

## BUG反馈

如果你在使用设计器的过程中有任何问题，可以通过以下方式来向我反馈。我会关注每一个问题。这对light chaser的开源社区非常重要。感谢给位！

- [GitHub Issue](https://github.com/xiaopujun/light-chaser/issues)
- [Gitee Issue](https://gitee.com/xiaopujun/light-chaser/issues)
- 邮箱：1182810784@qq.com
- 微信：P2016137144
- QQ：1182810784

## 性能问题

LIGHT CHASER支持在单页面上百个图表同时渲染。LIGHT
CHASER在渲染逻辑上已经做了代码设计上的优化。但是浏览器页面所能承载的资源是有限的。渲染元素越多理论上所需的计算渲染资源就更多，也会更卡。因此为了更好的性能表现，你可以尝试以下几种优化手段

- 压缩静态资源：比如使用本地图片或者服务器组件时，应该尽量压缩他们的体积
- 开启浏览器缓存：浏览器缓存可以减少网络请求，从而提高页面性能。
