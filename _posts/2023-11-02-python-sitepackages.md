---
title: vscode 가상환경 site-pacakages path 설정
author: yjh
categories: [Python, Trouble Shooting]
tags: [python, trouble shooting]
---

## 문제

몇 주 전부터 특정 딥러닝 모델 ([YOLOv8](https://ultralytics.com))을 predict 할 때마다 `segmentation fault` 발생으로 predict 가 되지 않았던 문제가 발생했다.

관련 모델 공식 홈페이지를 들어가기도해보고 [깃허브](https://github.com/ultralytics/ultralytics/issues) 도 확인해봤지만 공식 답변은 거의 항상 `pip install ultralytics` 로 귀결 됐다.

몇 번이고 삭제 후 재설치도 진행해보고 다른 가상환경을 파서 시도도 해봤지만 결과는 항상 동일했다.

그렇게 디버깅을 하던 중 `import torch` 부분에서 현재 내가 설정한 가상환경이 아닌 다른 가상환경의 `torch` 를 `import` 하는 것을 확인.

그러나 구글링해서 알아낸 `settings.json` 이나 `launch.json` 을 수정하는 방법으로는 해결 되지 않았었다.

## 해결

```shell
python -c "import sys; print('\n'.join(sys.path))"

conda deactivate
export PYTHONNOUSERSITE=1
conda activate <virtual envrioment name>
```

- `python ~ ...` 명령어로 `sys.path` 를 찍어보니 현재 가상환경이 아닌 다른 가상환경도 같이 물어오는 것을 확인
- `export PYTHONNOUSERSITE=1` 명령어로 `global packages` 사용을 하지 않도록 수정
- 전체 환경에 적용을 하고 싶다면 `.bashrc` 파일에 `export PYTHONNOUSERSITE=1` 를 추가해서 적용

---

- 위의 해결책은 일시적인 방법이었으며, 에러 내용을 좀 더 확인해본 결과 CUDA 관련 문제라 유추, CUDA 업데이트 이후 정상작동하는 것을 확인했다.
- nvidia-driver 부터 cuda, cudnn 전체 삭제 후 재설치를 진행하였다.

## Reference

- [lightman.log](https://velog.io@thk-lightman/Mac-Python-개발환경-설정가상환경-로컬-빅쿼리-연동)
