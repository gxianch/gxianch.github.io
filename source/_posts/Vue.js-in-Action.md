---
title: Vue.js-in-Action
date: 2020-12-19 16:47:01
categories: [Vue]
toc: true
mathjax: true
top: 98
tags:
  - ※※※  
  - ✰✰✰✰✰
  - Manning
  - Action
---



Action系列是必读书籍，内容质量有保证，英文版简单易读，推荐直接看英文版，笔记有点乱，懒得整理了，看懂源码示例就可以了

git源码地址：https://github.com/ErikCH/VuejsInActionCode

{% asset_img label cover.jpg %}
![](Vue.js-in-Action/cover.jpg)
<!-- more -->



- > With no outlet for the computed property, there’s nothing to update, and therefore noreason to enter the update cycle. The beforeUpdate function won’t be executed andthe corresponding message won’t be logged to the console.

- > Because the value of cartItemCount is the result of userinteraction, and not something that came from a database

- > As we know, the v­show directive hides or shows anelement using CSS, while the v­if/v­else directive removes the content from theDOM.

- > Here are several possible use cases where v­show is the rightchoice:

- > but at least one of them should always be showing

- > whether there’s afallback, or default, chunk of content you want to display.

- > differences are between the v­model directive and the v­bind directive that we used in chapter 2. Keep in mind thatthe v­model directive is used mainly for input and form binding.

- > The v­bind directive is mostly used to bind HTML attributes.

- > It’s worth mentioning that the v­model directive uses the v­bind directive behind thescenes. Let’s say you had . The v­model directiveis syntactic sugar for . Regardless, using the v­modeldirective is much easier to type and understand.

- > It’s worth mentioning that we could use an empty order object here and not define theproperties firstName and lastName explicitly inside it. Vue.js can implicitly add theproperties to the object.

- > this.$el. This will be the root DOMelement that the Vue instance is managing. From there you can run any type ofDocument method, like querySelector(), to retrieve any element you’d like.

- > The v­model directive has the .trim, .lazy, and .number modifiers. The .trimmodifier eliminates whitespace while the .number modifier typecasts strings asnumbers. The .lazy modifier changes when data is synced.

- > Vue.component('my­component', { 3 template: '
  >
  > Hello From Global Component
  >
  > ' 4 });
  >
  > 

- > const Component = { 1 template: '
  >
  > Hello From Local Component
  >
  > ' 2 }; new Vue({ el: '#app', components: {'my­component': Component} 3 });
  >
  > 

- > When the parent updates properties, it flows down to the child component andnot the other way around.

- > Note that all values are passed by reference. If an object or array is mutated in the child,it will affect the parent state.

- > Theevent interface can listen to events using the $on(eventname) and trigger eventsusing $emit(eventName). Typically, $on(eventname) is used when sending eventsbetween different components that aren’t parent and child. For parent and child events,we must use the v­on directive. We can use this interface so parent components canlisten directly to child components.

- > Because we’re no longer usingthe passed­in props, we can delete them from the form component.

  