---
layout: single
categories:
  - Android
title: RecyclerView Performance Improving
author: Gold
author-email: pnurep@gmail.com
description: 리사이클러뷰 성능 향상에 대해 알아봅니다
tags:
  - Android
  - RecyclerView
  - PreCache
  - Prefetch
last_modified_at: 2019-03-11T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
---

<br>

# RecyclerView Performance Improving

안드로이드 RecyclerView 의 성능 향상에 대해 알아봅니다.

<br>


## Recyclerview.setHasFixedSize(true)
이 방법을 사용하면 RecyclerView에서 RecyclerView를 추가하거나 제거 할 때마다 항목 크기를 계산하지 않도록 지시한다.


## ItemViewCacheSize

```java
RecyclerView.setItemViewCacheSize(int n)
```
잠재적으로 공유된 재생 보기 풀에 추가하기 전에 유지할 화면 보기 수를 설정한다.

오프스크린 뷰 캐시는 연결된 어댑터의 변경 사항을 계속 인식하므로, LayoutManager 가 수정되지 않은 그 뷰들을 rebind 하기위해 어댑터로 다시 반환할 필요 없이 재사용할 수 있게 허가한다.

다시말하면, 리사이클러뷰를 스크롤 했을때 뷰는 거의 화면밖으로 사라지게 되는데, 리사이클러뷰가 그 뷰를 계속 유지하는 것으로 onBindViewHolder() 메서드의 재실행 없이 그 뷰로 다시 돌아올 수 있다.

<br>


## setHasStableIds

```Adapter.setHasStableIds(true)``` 와 어댑터 내에서 아래의 콜백메서드를 추가한다.
```kotlin
override fun getItemId(position: Int): Long { return position.toLong() }
```


동일한 데이터를 자주 표시하는 리스트이거나 notifyDataSetChanged() 가 빈번하게 불린다면 이 방법으로 성능이 크게 개선될 수 있다.

만약 데이터 중 같은 id 값을 리턴하는 데이터가 있다면 그 경우에는 onBindViewHolder() 가 호출되지 않는다.
ex) 0 번째 포지션의 아이템과 5 번째 포지션의 아이템이 getItemId() 메서드로 리턴하는 id 값이 같다면 5 번째 포지션에서는 onBindViewHolder() 가 호출되지 않는다. 0 번째와 5 번째를 같은 데이터로 취급하여 바인딩을 할 필요가 없고 그만큼 메서드 호출이 일어나지 않기 때문에 성능이 향상된다.

즉, 5 를 제외한 -> 0, 1, 2, 3, 4, 6, 7... 만 onBindViewHolder() 가 호출된다.

데이터에 변동이 있는 경우에도 성능향상을 이룰 수 있다. 기존데이터의 3 번 포지션에 데이터를 추가했다고 가정하자. 일반적으로는 전체의 데이터셋에 대해 notify 가 일어나지만 stableId 가 true 로 설정된 상황이라면 이미 호출된 stable Id 를 제외한 포지션만이 호출된다.

즉, 기존의 notify 방식(전체 데이터에 대해 변경확인) 의 동작이 아닌, 변경되는 부분(Id) 에만 한정해서 그 포지션에만 onBindViewHolder() 를 호출하게 함으로써 성능향상을 노린다.


<br>



## PreCache

RecyclerView 는 기본적으로 보이는 화면영역과 그 위 아래로 약간의 추가 영역에 대한 크기를 내부적으로 갖고 그 영역크기에 맞는양의 뷰를 유지한다. RecyclerView 가 갖는 화면영역을 가상으로 더 넓힐수 있다면 default 로 갖는 아이템의 양보다 더 많은양을 미리 로드해 놓을 수 있기 때문에 스크롤타임에 새로운 뷰에 대한 onCreateViewHolder() 의 호출을 줄여 좀 더 스무스한 스크롤이 가능하다.


```kotlin
val manager = PreCachingLayoutManager(this@MainActivity).apply {
    orientation = LinearLayoutManager.VERTICAL
    setExtraLayoutSpace(DeviceUtils.getScreenHeight(this@MainActivity))
}

recyclerview.layoutmanager = manager
```

---

```java
public class PreCachingLayoutManager extends LinearLayoutManager {
    private static final int DEFAULTEXTRALAYOUT_SPACE = 600;
    private int extraLayoutSpace = -1;

    public PreCachingLayoutManager(Context context) {
        super(context);
    }

    public void setExtraLayoutSpace(int extraLayoutSpace) {
        this.extraLayoutSpace = extraLayoutSpace;
    }

    @Override
    protected int getExtraLayoutSpace(RecyclerView.State state) {
        if (extraLayoutSpace > 0) {
            return extraLayoutSpace;
        }
        return DEFAULTEXTRALAYOUT_SPACE;
    }
}
```

---

```java
public class DeviceUtils {
    public static int getScreenHeight(Activity activity) {
        DisplayMetrics displayMetrics = new DisplayMetrics();
        activity.getWindowManager().getDefaultDisplay().getMetrics(displayMetrics);

        return displayMetrics.heightPixels;
    }
}
```



하지만 추가적인 레이아웃 스페이스를 확장하는 것은 높은 코스트를 요구하므로 적재적소에 사용하면 되겠다.


<br>


## Prefetch

Prefetch 는 미리 데이터를 로드 한다는 점에는 PreCache 와 유사한 방법이라고 할 수 있다. 하지만 PreCache 자신의 영역을 확장시켜 많은 데이터를 미리 가져오고 미리 생성한다면 Prefetch 는 데이터를 몇 프레임 앞서 미리 onBindViewHolder() 호출하는 방식이다.

UI 스레드에서 뷰의 inflate, bind 가 끝나면 GPU Render 스레드에서 그 뷰에 대한 렌더링 작업을 하는데 그 동안에 UI스레드는 유휴상태가 된다. 이 방식은 스크롤시에 문제가 되는데, 스크롤시에 새로운 뷰에 대한 inflate 작업이 필요할 경우 다시 UI 스레드는 뷰의 inflate, bind 작업에 들어가는데 이 때가 GPU Render 스레드에서 렌더링이 끝나고 컨텐츠가 막 표시되려는 타이밍과 겹친게 된다. 뷰의 inflate 와 bind 는 고 코스트의 작업이므로 이렇게 작업이 겹치는 경우 부드러운 렌더링이 불가능하다.

Prefetch 방식은 이런 문제를 해결하고자 스크롤시에 새로운 뷰의 inflate 가 필요한 경우, UI 스레드가 유휴상태에 들어가는 그 기간동안 미리 몇 프레임 앞서 onBindViewHolder() 를 호출한다.

Prefetch 는 RecyclerView 25.0.0 이상에는 기본적으로 true 로 설정되어 있고 ViewCache 메모리를 추가적으로 소모하기 때문에 LayoutManager.setItemPrefetchEnabled(false) 로 비활성화 할 수 있다.


[참고할 만한 자료](https://medium.com/google-developers/recyclerview-prefetch-c2f269075710)




<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}


























