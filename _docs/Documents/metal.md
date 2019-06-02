---
title: Metal
permalink: /docs/metal/
---

고급 3D 그래픽을 렌더링하고 GPU를 사용하여 데이터 병렬 계산 수행.

### Overview

그래픽 프로세서(GPU)는 그래픽을 빠르게 렌더링하고 데이터 병렬 계산을 수행하도록 설계되었다. 장치에서 사용 가능한 GPU와 직접 통신해야하는 경우 Metal 프레임 워크를 사용하면 된다. 복잡한 장면을 렌더링하거나 과학 계산을 수행하는 앱은 이 성능을 사용하여 최대 성능을 얻을 수 있다.
Such apps include:
- 정교한 3D 환경을 구현하는 게임
- Final Cut Pro와 같은 비디오 처리 응용 프로그램
- 과학 연구를 수행하는 데 사용되는 것과 같은 Data-crunching apps.

Metal은 기능을 보완하는 다른 프레임 워크와 함께 작동한다. MetalKit은 화면에 Metal 컨텐츠를 가져 오는 작업을 단순화한다. Metal Performance Shaders를 사용하여 사용자 정의 렌더링 기능을 구현하거나 기존 기능의 대형 라이브러리를 활용할 수 있다.
Core Image, SpriteKit 및 SceneKit을 포함하여 많은 상위 수준의 Apple 프레임 워크가 Metal 위에 구축되어 성능을 활용한다. MetalKit을 사용하면 GPU 프로그래밍의 세부 사항을 피할 수 있지만 Custom Metal 코드를 작성하면 최고 수준의 성능을 얻을 수 있다.

### Getting the Default GPU

Metal 프레임워크를 사용하려면 GPU device를 얻어야 한다. 앱에서 Metal과 상호 작용해야하는 모든 객체는 런타임에 MTLDevice에서 가져온다:

```swift
guard let device = MTLCreateSystemDefaultDevice() else {
    fatalError( "Failed to get the system's default Metal device." ) 
}
```

### Setting Up a Command Structure

GPU가 사용자를 대신하여 작업을 수행하게하려면 명령을 보내야 한다. 명령은 응용 프로그램에서 필요로하는 그리기, 병렬 계산 또는 자원 관리 작업을 수행한다.
Metal 앱과 GPU의 관계는 클라이언트 - 서버 패턴의 관계이다:

- Metal 앱은 클라이언트.
- GPU는 서버.
- GPU에 명령을 보내 요청.
- 명령을 처리 한 후, GPU는 다음 작업이 준비되면 알려준다.

**Figure 1** Client-server usage pattern when using Metal. 

![Client-server usage pattern when using Metal](https://docs-assets.developer.apple.com/published/861974e544/6411df7f-4f5c-46d6-9573-c2c6dd10fffb.png)

GPU에 명령을 보내려면, commandEncoder 오버젝트를 사용하여 commandBuffer에 추가 해야한다. commndQueue에 commandBuffer를 추가하고 Metal이 commandBuffer의 명령을 실행할 준비가 되면 commandBuffer를 commit한다. commandBuffer에 명령을 넣고 대기열에 넣고 commit하는 순서는 실행 순서에 영향을 주기때문에 중요하다. 

##### Make Initialization - Time Objects

ommandQueue 와 pipeline 객체는 처음 초기화할때 생성하고 일반적으로 무한정 유지한다. 초기화하는데 비용이 많이 들지만 한번 초기화되면 재사용이 빠르다.

##### Make a Command Queue

command queue를 생성하려면, device의 makeCommandQueue() 함수를 호출한다:

```swift
commandQueue = device.makeCommandQueue()
```

일반적으로 command queue는 재사용하기 때문에 strong reference로 만들어야 한다. 다음과 같이 command queue를 사용하여 command buffer를 유지한다.:

**Figure 2** Your app's command queue

![command-queue](https://docs-assets.developer.apple.com/published/67e10c53e3/27dc137c-d852-461e-aff2-e93ab71f44e9.png)

##### Make One or More Pipeline Objects

pipeline object는 Metal에게 명령을 처리하는 방법을 알려준다. pipeline object는 Metal shading language로 작성한 함수를 캡슐화한다. Metal workflow에 pipeline을 적용하는 방법은 다음과 같다:

- 데이터를 처리하는 Metal 쉐이더 함수를 작성한다.
- 쉐이더가 포함된 pipeline 객체 생성.
- pipeline 활성화.
- 그리기, 계산 또는 블릿 호출.

Metal은 즉시 그리기, 계산 또는 블릿 호출을 수행하지 않는다. 대신 인코더 개체를 사용하여 해당 호출을 command buffer에 캡슐화하는 명령을 삽입한다. command buffer를 commit 한 후에, Metal은 그것을 GPU로 보내고 활성 pipeline 객체를 사용하여 명령을 처리한다.

**Figure 3** The active pipeline on the GPU containing your custom shader code that processes commands.

![pipeline-object](https://docs-assets.developer.apple.com/published/746b9057d5/9e68fba0-6dd4-4bc6-92a9-da1646f742d4.png)

##### Issue Commands to the GPU

command queue와 pipeline이 설정된 상태에서 GPU에 명령을 내릴 차례이다. 따라야 할 과정은 다음과 같다.

- command buffer 생성
- 버퍼를 명령으로 채움
- command buffer 를 GPU에 commit

렌더링 루프의 일부로 애니메이션을 수행하는 경우 애니메이션의 모든 프레임에 대해이 작업을 수행한다. 또한이 프로세스를 따라 일회용 이미지 처리 또는 기계 학습 작업을 실행.

##### Create a Command Buffer

commandQueue의 makeCommandBuffer() 함수를 호출해서 command buffer를 생성:

```swift
guard let commandBuffer = commandQueue.makeCommandBuffer() else { return }
```

command 와 commandBuffer의 관계

**Figure 4** A command buffer's relationship to the commands it contains.

![commandbuffer-command](https://docs-assets.developer.apple.com/published/25bef2712d/5b88782f-d94e-45a2-833c-c8570dbb7b84.png)

##### Add Commands to the Command Buffer

인코더 객체에서 (draw, compute or blit)작업 관련 기능을 호출하면 인코더는 해당 호출에 해당하는 명령을 command buffer에 배치한다. 인코더는 GPU가 런타임에 작업을 처리하는 데 필요한 모든 것을 포함하도록 명령을 인코딩한다.

**Figure 5** Command encoder inserting commands into a command buffer as the result of a draw.

![commandEncoder-inserting](https://docs-assets.developer.apple.com/published/2652788410/3741a51f-357e-467c-adaa-82d8f533db06.png)

task에 따라 MTLCommandEncoder의 구체적인 하위 클래스로 실제 명령을 인코딩 할 수 있다.

- MTLRenderCommandEncoder : 렌더링
- MTLComputeCommandEncoder : 병렬 계산
- MTLBlitCommandEncoder : 자원 관리 

##### Commit a Command Buffer

명령을 실행하려면 GPU에 command buffer를 commit 한다:

```swift
commandBuffer.commit()
```

command buffer를 커미팅하고 명령이 즉시 실행되지는 않는다. 대신 Metal은 대기열에서 대기중인 이전 command buffer를 커밋한 후에만 ​​버퍼의 명령을 실행하도록 예약한다. command buffer를 명시 적으로 대기열에 추가하지 않은 경우 Metal은 버퍼를 커밋하면이를 수행한다.

버퍼는 커밋 된 후에 재사용하지 않지만 스케줄링, 완료 또는 상태 쿼리에 대한 notification을 선택할 수 있다.



