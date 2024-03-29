---
title: "RecyclerView に android:requiresFadingEdge を設定すると境界の縁が少しオサレになる"
emoji: "🌫️"
type: "tech"
topics: ["android", "kotlin", "recyclerview"]
published: true
---

[RecyclerView](https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView) に [android:requiresFadingEdge](https://developer.android.com/reference/android/view/View#attr_android:requiresFadingEdge) を設定するとスクロールで途切れる境目に徐々に薄れる演出が追加され、スクロールが可能であることを表現することができます。
[android:fadingEdgeLength](https://developer.android.com/reference/android/view/View#attr_android:fadingEdgeLength) で長さを設定できるようですが、長すぎるとフェードの不自然な表示/非表示の切り替わりが見え易くなるため大きめの値は指定しない方が無難そうです 🧐

## サンプル

```xml:list_item.xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

  <TextView
    android:id="@+id/text_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:textAppearance="@style/TextAppearance.AppCompat.Large" />
</layout>
```

```xml:activity_main.xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  xmlns:tools="http://schemas.android.com/tools">

  <androidx.recyclerview.widget.RecyclerView
    android:id="@+id/recycler_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:requiresFadingEdge="vertical"
    app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
    tools:context=".MainActivity"
    tools:listitem="@layout/list_item" />
</layout>
```

```kotlin:RecyclerViewBindingViewHolder.kt
abstract class RecyclerViewBindingViewHolder<out T : ViewDataBinding>(val binding: T) :
  RecyclerView.ViewHolder(binding.root) {

  constructor(parent: ViewGroup, @LayoutRes layoutId: Int) : this(
    DataBindingUtil.inflate<T>(LayoutInflater.from(parent.context), layoutId, parent, false)
  )
}
```

```kt:TextListAdapter.kt
/**
 * 文字列表示用の [ListAdapter]
 */
class TextListAdapter : ListAdapter<String, RecyclerViewBindingViewHolder<*>>(
  object : DiffUtil.ItemCallback<String>() {
    override fun areItemsTheSame(oldItem: String, newItem: String) = oldItem == newItem

    override fun areContentsTheSame(oldItem: String, newItem: String) = oldItem == newItem
  }
) {

  override fun onCreateViewHolder(
    parent: ViewGroup,
    viewType: Int
  ): RecyclerViewBindingViewHolder<*> = ListItemViewHolder(parent)

  override fun onBindViewHolder(holder: RecyclerViewBindingViewHolder<*>, position: Int) {
    if (holder is ListItemViewHolder) {
      holder.bind(getItem(position))
    }
  }

  /**
   * セルの [RecyclerViewBindingViewHolder]
   * 
   * @param parent [ViewGroup]
   */
  private inner class ListItemViewHolder(parent: ViewGroup) :
    RecyclerViewBindingViewHolder<ListItemBinding>(parent, R.layout.list_item) {

    fun bind(text: String) {
      binding.textView.text = text
    }
  }
}
```

```kt:MainActivity.kt
class MainActivity : AppCompatActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)
    val adapter = TextListAdapter()
    adapter.submitList(
      listOf(
        "花震ふ 富士山 火山性微動",
        "銀盤の 弧の凍りゆく 明けの星",
        "右肩に 枯野の冷気 7号車",
        "マンモスの 滅んだ理由 ソーダ水",
        "エルメスの騎士像 翳りゆき驟雨",
        "庖丁始 都心は 計画運休",
        "雪原や 星を指す 大樹の骸",
        "もてなしの 豆腐ぶら下げ 風の盆",
        "野良犬の 吠える沼尻 花筏",
        "凍蠅よ 生産性の 我にあるや",
        "まるでシンバル 移り来し街 余寒",
        "テーブルに 君の丸みの マスクかな",
        "行間に 次頁の影 夕立晴",
        "羊群の最後は すすき持つ少年",
        "セイウチの 麻酔の効き目 夏の空",
        "旱星 ラジオは余震 しらせおり",
        "廃村の ポストに小鳥 来て夜明け",
        "道化師の ギャロップのごと 牧開",
        "万緑に 提げて 遺品の紙袋",
        "連覇のさきぶれ 沸き立つ初電車",
      )
    )
    binding.recyclerView.adapter = adapter
  }
}
```

## 実行結果

![](https://storage.googleapis.com/zenn-user-upload/n8mpcrkbby1askubo0ig3xxkvuqp)
*android:requiresFadingEdge="vertical"*

## 未設定の場合

![](https://storage.googleapis.com/zenn-user-upload/h5qphc4ch3cyvky389fia4879ru3)
*android:requiresFadingEdge="none"*
