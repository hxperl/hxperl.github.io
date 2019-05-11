---
layout: post
title:  "Multi Threading"
date:   2019-05-11 23:38:09
author: hxperl
---

# UIImage vs CGImage vs CIImage

상위 수준에서 볼 때 UIKit, CoreImage, CoreGraphics 각각 근본적으로 다른 것들을 수행하기 때문에 모두가 통일 된 이미지 형식이 될 수 없다.
그러나 이러한 프레임워크들은 서로 변환이 빠르고 최적화가 잘되어 있다.

### UIImage

Apple은 이미지 데이터를 표시하는 high-level 방법으로 UIImage 객체를 설명한다. 파일이나 Quartz image objects, raw 이미지 데이터로부터 UIImage를 생성 할수 있다.
UIImage는 immutable하면서, 초기화시에 이미지의 프로퍼티을 지정할 필요가 있다. 이것은 UIImage 객체가 모든 스레드에서 안전하게 사용된다는 것을 의미한다.

### CIImage

CIImage는 이미지를 나타내는 immutable object이지만, 관련된 데이터만 있을 뿐 이미지는 아니다. 이미지를 생성하는데 필요한 모든 정보를 담고 있다.
일반적으로 CIImage 객체는 CIFilter, CIContext, CIColor 및 CIVector와 같은 다른 Core Image 클래스와 함께 사용된다. Quartz 2D 이미지, 코어 비디오 이미지 등과 같은 다양한 소스에서 제공되는 데이터로 CIImage 객체를 생성 할 수 있다.

 - 다양한 GPU에 최적화 된 코어 이미지 필터를 사용해야한다. 
 - NSBitmapImageReps로 변환 할 수도 있다. 
 - CPU 또는 GPU를 기반으로 할 수 있다.

### CGImage

CGImage는 비트 맵만 나타낼 수 있다. 블렌드 모드 및 마스킹과 같은 CoreGraphics의 작업에는 CGImageRefs가 필요하다. 실제 비트 맵 데이터에 액세스하고 변경해야하는 경우 CGImage를 사용할 수 있다. 또한 NSBitmapImageReps로 변환 될 수 있다.