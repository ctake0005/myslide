## StreetView avoid indoor locations
2018/01/11 shibuya.apk #21  
@ctake0005

---
## About me
<img src="/assets/images/twitter.png" width="60">
- @ctake0005

---
## What I want to do
- （駅まで歩く人のために） Google Map で指し示した駅の外観（可能であれば出入り口）を表示したい！
<img src="/street-view-avoid-indoor/images/spec-image.png" width="600">

---
## How to use StreetView API (1)
- StreetViewPanoramaFragment をレイアウトに追加

```xml
<fragment
    android:name="c.g.a.gms.maps.StreetViewPanoramaFragment"
    android:id="@+id/streetviewpanorama"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

---
## How to use StreetView API (2)
- OnStreetViewPanoramaReadyCallback インターフェースを実装

```
public class MainActivity extends FragmentActivity
    implements OnStreetViewPanoramaReadyCallback {
    ...
}
```

- getStreetViewPanoramaAsync() で、コールバックを設定

```
StreetViewPanoramaFragment streetViewPanoramaFragment =
    (StreetViewPanoramaFragment) getFragmentManager()
        .findFragmentById(R.id.streetviewpanorama);
streetViewPanoramaFragment.getStreetViewPanoramaAsync(this);
```

---
## How to use StreetView API (3)
- 準備ができたら onStreetViewPanoramaReady(StreetViewPanorama) がコールバックされるので、StreetViewPanorama に対し、setPosition() を呼ぶとその位置のストリートビューが表示される

```
@Override
public void onStreetViewPanoramaReady(StreetViewPanorama panorama) {
    panorama.setPosition(new LatLng(-33.87365, 151.20689));
}
```

→ 簡単 😃

---
## Problem
- setPosition() で指定した位置のストリートビューの屋外/屋内がわからない(指定もできない)
    - https://issuetracker.google.com/issues/35824536
<img src="/street-view-avoid-indoor/images/issue.png" width="600">

---
## My Solution
- **Google Maps Roads API > nearestRoads**
- 指定座標から最も近い道路上の区間を返してくれるAPI

---
## But...
- 付近に道路がない場合は、位置が返却されない
    - ターミナル駅などでは、検索対象半径に道路が入らないことがある
- 道路と地下鉄構内などが重なっている場合、道路上の座標を指定しても屋内Viewになってしまうことがある
- 従量課金API (2500 req/day)

---
## Conclusion
- アプリでストリートビューを表示することは簡単
- Roads API の nearestRoads を使用することで簡易的に **なるべく** 屋外のストリートビューになるようにすることは可能
- よりうまい方法を知っている方がいたら教えて欲しいです 🙏
