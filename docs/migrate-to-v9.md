---
id: 'migration-v9'
title: 'Migrate to v9'
sidebar_label: '🚀 Migrate to v9'
---

## What is new in v9

Say hello to addons! What are addons? So, addons are like DLCs in video games but for react-toastify 😆. More seriously, you can think of it as utilities built around react-toastify. For example, custom theme, hooks, components etc...

### useNotificationCenter

The first addon that I would like to introduce is the `useNotificationCenter` headless hook! As the name suggests, it let you build your notification center on top of react-toastify. See for yourself 👇

<iframe src="https://codesandbox.io/embed/notification-center-framer-vddoj5?fontsize=14&hidenavigation=1&hidedevtools=1&view=preview&codemirror=1&theme=dark"
     style={
       {
            width:"100%",
            height: "700px",
            border:0,
          borderRadius: "4px",
          overflow:"hidden"
       }}
     title="notification-center-framer"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   />

Another example using MUI.

<iframe src="https://codesandbox.io/embed/mui-notification-center-zvxod3?fontsize=14&hidenavigation=1&hidedevtools=1&view=preview&codemirror=1&theme=dark"
     style={
       {
            width:"100%",
            height: "700px",
            border:0,
          borderRadius: "4px",
          overflow:"hidden"
       }}
     title="mui-notification-center"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   />

:::tip
Addons only impact your bundle size if you use them 🎉!
:::

Check the [documentation](/react-toastify/addons/use-notification-center) for more details. 

### Stacked toasts

This second addon will be released later. There are a bunch of details that I need to cover and I don't want to release something too buggy. Nevertheless, I'm really excited about it and I think it's worth showcasing anyway. 

I call it `StackedContainer` for now, it's an alternative to the `ToastContainer` component.

![stacked-container](https://user-images.githubusercontent.com/5574267/160688000-1d01d949-d9e1-41f4-858c-f5c9a33b901d.gif)


## Breaking changes

There are 2 breaking changes. The API change for `toast.onChange` and the removal of `toast.configure`.

### `toast.onChange`

The previous API was returning the `numberOfToastDisplayed` and the `containerId`. Honestly, this API seems to be incomplete. 

For example, with the old API, if I wanted to do some logging this would be very difficult because I don't have enough data to log.

```tsx
toast.onChange((numberOfToastDisplayed, containerId) => {
  logger.info("nothing useful to log, ...")
})
```

The new API offers more possibilities. The callback will receive a `ToastItem`. The item provides a bunch of useful properties `status`, `content`, `id`, `data`, etc...


```jsx
interface ToastItem<Data = {}> {
    id: Id;
    content: React.ReactNode;
    theme?: Theme;
    type?: TypeOptions;
    isLoading?: boolean;
    containerId?: Id;
    data: Data;
    icon?: React.ReactNode | false;
    status: "added" | "removed" | "updated" 
}

const unsubscribe = toast.onChange((payload: ToastItem) => {
  switch (payload.status) {
    case "added":
      // new toast added
      break;
    case "updated":
      // toast updated
      break;
    case "removed":
      // toast has been removed
      break;
  }
});
```

For example, if I want to log something every time there is a new error notification, with the new API it's trivial

```tsx
toast.onChange(payload => {
  if(payload.status === "added" && payload.type === toast.TYPE.ERROR) {
    logger.error({
      createdAt: Date.now(),
      content: payload.content,
      data: payload.data
    })
  }
})
```

### `toast.configure` removal

`toast.configure` works fine for most cases but the current implementation has one main issue. It does not work with react context because it creates a new react tree.
That being said, having 2 APIs to do the same thing is a bad thing. 

## Bug fixes

- [#725](https://github.com/fkhadra/react-toastify/issues/725) the success toast on promise does not disappear when resolving too quickly
- [#711](https://github.com/fkhadra/react-toastify/issues/711) updated toast sometimes has wrong styles
-  [#700](https://github.com/fkhadra/react-toastify/issues/700) generics are not used for toast.promise's result type if you use a custom render method