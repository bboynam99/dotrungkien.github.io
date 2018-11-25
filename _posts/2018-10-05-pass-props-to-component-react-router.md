---
layout: post
title: 'Cách truyền props cho component trong React Router'
mathjax: true
---

Dạo gần đây mình có làm một project sử dụng react, và gặp phải một vấn đề nho nhỏ - truyền thêm props vào Route. Vấn đề không khó, nhưng khi đọc giải pháp mình thấy cách suy nghĩ để đi đến giải quyết vấn đề khá thú vị nên muốn chia sẻ lại với các bạn.

Nhắc lại một chút, thông thường ta sẽ sử dụng React Router để tạo một Route component giống như sau:

```js
<Route path='/dashboard' component={Dashboard} />
```

ban đầu mình suy nghĩ rất đơn giản, ok truyền thêm tham số vào như **props** thông thường thôi:

```js
<Route path='/dashboard' component={Dashboard} isAuthed={true} />
```

rất tiếc, trông code có vẻ đẹp nhưng không chạy :sad: Lý do rất đơn giản, React Router không forward các props khác của bạn đi, ngoại trừ _component_

hmmm, ô kê, vậy thì giải pháp ở đây là biến bản thân object Dashboard thành một prop component mới & truyền props bên trong component đó, sound good!

```js
<Route path='/dashboard' component={() => <Dashboard isAuthed={true} />} />
```

ok, code đã chạy. Tuy nhiên nhìn lại, giải pháp này có điều không ổn. Theo official docs của React thì

> “When you use the component props, the router uses React.createElement to create a new React element from the given component. That means if you provide an inline function to the component attribute, you would create a new component every render. This results in the existing component unmounting and the new component mounting instead of just updating the existing component.”

có nghĩa là mỗi lần render, chúng ta đều phải unmount và mount lại component `Dashboard`, đây rõ ràng là một giải pháp không hề tốt về performance.

Vậy giải pháp tốt hơn ở đây là gì ?

Câu trả lời là sử dụng prop `render` thay vì `component`.

`render` và `component` đều chấp nhận dạng functional, nhưng điểm tạo nên sự khác biệt ở đây chính là `render` _không cần phải unmount_ component. Code của chúng ta giờ sẽ như sau:

```js
<Route
  path='/dashboard'
  render={props => <Dashboard {...props} isAuthed={true} />}
/>
```

Tuyệt vời, một giải pháp rất đơn giản cho vấn đề truyền props trong Route. Nếu bạn có cách làm nào hay hơn, hãy chia sẻ cho mình nhé.

Tham khảo:

- [https://tylermcginnis.com/react-router-pass-props-to-components/](https://tylermcginnis.com/react-router-pass-props-to-components/)
