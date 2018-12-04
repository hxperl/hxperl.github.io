---
layout: post
title:  "Get iPhone X keyboard height"
date:   2018-12-04 15:57:09
author: hxperl
---



# iPhone X keyboard height control

일반적으로 iPhone 키보드 높이를 구하기 위해 사용 되는 코드

```swift
if let keyboardSize = (notification.userInfo?[UIResponder.keyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue,  fixHeight == false {
            var keyboardHeight = keyboardSize.height
            if #available(iOS 11.0, *) {
                let bottomInset = view.safeAreaInsets.bottom
                keyboardHeight -= bottomInset
            }
        }
```

iPhone X 이상 부터

앱을 켜서 처음 시작할때와 그 이후 키보드 높이가 달라 UI가 깨지는 현상이 생길 수 있다.

Replace `UIResponder.keyboardFrameBeginUserInfoKey`

with `UIResponder.keyboardFrameEndUserInfoKey`

Apple Docs

> UIKeyboardFrameBeginUserInfoKey- The key for an NSValue object containing a CGRect that identifies the start frame of the keyboard in screen coordinates.
>
> UIKeyboardFrameEndUserInfoKey - The key for an NSValue object containing a CGRect that identifies the end frame of the keyboard in screen coordinates.