

`UIView`의 `frame`을 설정할 때의 `width`와 `height` 값은 그 뷰의 초기 크기를 결정합니다. 하지만 이후에 Auto Layout 제약 조건이 적용되면, 초기에 설정한 `frame`의 값은 무시될 수 있습니다. Auto Layout이 활성화되면 (즉, `translatesAutoresizingMaskIntoConstraints`가 `false`로 설정되면), 뷰의 크기와 위치는 제약 조건에 따라 결정됩니다.

여러분의 코드에서, `view2`와 `view3`에 대한 `widthAnchor`와 `heightAnchor` 제약 조건을 설정하지 않았기 때문에, 그들의 크기는 0으로 간주되어 보이지 않게 됩니다. 

`view2`와 `view3`가 화면에 보이도록 하려면, 그들에 대한 `width`와 `height`에 대한 제약 조건을 반드시 설정해야 합니다. 초기에 설정한 `frame`의 크기는 Auto Layout이 활성화될 때 무시됩니다.