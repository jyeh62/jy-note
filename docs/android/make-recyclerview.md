---
type: android
keywords: recyclerview
---

# Make RecyclerView

## RecyclerView

RecyclerView.Adapter<ViewHolder>를 상속 받는 Adapter 구현

### 구현 메소드

- onCreateViewHolder - LayoutInflater를 사용하여 item layout을 생성하고, ViewHolder를 생성
- onBindViewHolder - ViewHolder에서 가지고 있는 View들에 실제 데이터를 매핑
- getItemCount - DataSet의 갯수를 리턴

## ViewHolder

- RecyclerView.ViewHolder를 상속 받아서 구현
- 재활용하기 위해 Item을 표시하기 위한 View 를 (소유)가지고 있는 클래스, View 생성에 필요한 리소스를 절약할 수 있음