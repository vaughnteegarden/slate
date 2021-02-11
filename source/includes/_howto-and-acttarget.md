## Handling SSP Act Targets - Android

Acts and Information Pages are closely related. An information page simply displays the merchant's message to the consumer.  An Act is like a info page that includes a form the user must fill out. 

Before displaying `sspActResult`,  check the value of the field `sspActResult.sspAct.pageBuildingBlocks`. If it is not null and is not empty, it means that engagement owner has designed custom layout to present it. Here is how to handle it.


### 1. Create helper data class that holds both block and answer (if applicable):

```kotlin
data class BlockWrapper(
        val pageBuildingBlock: PageBuildingBlock,
        var answerToDisplay: String? = null
)
```

### 2. Create an adapter class to show the custom layout. 

```kotlin
class SspActBlockAdapter : ListAdapter<BlockWrapper, SspActBlockAdapter.ViewHolder>(BlockWrapperItemDiff) {

    private lateinit var layoutInflater: LayoutInflater

    lateinit var eventListener: SspActBlockEventListener

    private val blockSelectListener = object : SspBlockSelectClickListener {
        override fun onOptionSelected(blockWrapper: BlockWrapper, option: SelectionOption) {
            eventListener.onSelectBlockOptionSelected(blockWrapper, option)
        }
    }

    private val blockDateListener = object : SspBlockDateFieldListener {
        override fun onDateBlockClick(blockWrapper: BlockWrapper) {
            eventListener.onDateBlockSelected(blockWrapper)
        }
    }

    private val blockTextInputListener = object : SspBlockTextInputListner {
        override fun onTextInputChanged(blockWrapper: BlockWrapper, text: String) {
            eventListener.onTextInputBlockChange(blockWrapper, text)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        if(!::layoutInflater.isInitialized) {
            layoutInflater = LayoutInflater.from(parent.context)
        }

        return when(viewType) {
            VIEW_TYPE_HEADER -> HeaderViewHolder(ItemSspBlockHeaderBinding.inflate(layoutInflater, parent, false))
            VIEW_TYPE_PARAGRAPH -> ParagraphViewHolder(ItemSspBlockParagraphBinding.inflate(layoutInflater, parent, false))
            VIEW_TYPE_DIVIDER -> DividerViewHolder(ItemSspBlockDividerBinding.inflate(layoutInflater, parent, false))
            VIEW_TYPE_IMAGE -> ImageViewHolder(ItemSspBlockImageBinding.inflate(layoutInflater, parent, false))
            VIEW_TYPE_VIDEO -> VideoViewHolder(ItemSspBlockVideoBinding.inflate(layoutInflater, parent, false))
            VIEW_TYPE_DATE_FIELD -> DateFieldViewHolder(ItemSspBlockDateFieldBinding.inflate(layoutInflater, parent, false), blockDateListener)
            VIEW_TYPE_SELECT -> SelectViewHolder(ItemSspBlockSelectBinding.inflate(layoutInflater, parent, false), blockSelectListener)
            VIEW_TYPE_TEXT_INPUT -> TextFieldViewHolder(ItemSspBlockTextInputBinding.inflate(layoutInflater, parent, false), blockTextInputListener)
            else -> throw IllegalArgumentException("Invalid viewType: $viewType")
        }
    }

    override fun onBindViewHolder(viewHolder: ViewHolder, position: Int) {
        viewHolder.bind(getItem(position), position)
    }

    override fun getItemViewType(position: Int): Int {
        return when(getItem(position).pageBuildingBlock.type) {
            Type.HEADER -> VIEW_TYPE_HEADER
            Type.PARAGRAPH -> VIEW_TYPE_PARAGRAPH
            Type.DIVIDER -> VIEW_TYPE_DIVIDER
            Type.IMAGE -> VIEW_TYPE_IMAGE
            Type.VIDEO -> VIEW_TYPE_VIDEO
            Type.DATE_FIELD -> VIEW_TYPE_DATE_FIELD
            Type.SELECT -> VIEW_TYPE_SELECT
            Type.TEXT_FIELD, Type.UNKNOWN -> VIEW_TYPE_TEXT_INPUT
        }
    }

    abstract class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {

        protected val context: Context = itemView.context

        abstract fun bind(item: BlockWrapper, position: Int)
    }

    class HeaderViewHolder(private val binding : ItemSspBlockHeaderBinding) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
        }
    }

    class ParagraphViewHolder(private val binding : ItemSspBlockParagraphBinding) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
        }
    }

    class DividerViewHolder(private val binding : ItemSspBlockDividerBinding) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) { }
    }

    class ImageViewHolder(private val binding : ItemSspBlockImageBinding) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
        }
    }

    class VideoViewHolder(private val binding : ItemSspBlockVideoBinding) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
        }
    }

    class DateFieldViewHolder(private val binding : ItemSspBlockDateFieldBinding, private val listener: SspBlockDateFieldListener) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
            binding.listener = listener
        }
    }

    class SelectViewHolder(private val binding : ItemSspBlockSelectBinding, private val listener: SspBlockSelectClickListener) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
            binding.listener = listener
        }
    }

    class TextFieldViewHolder(private val binding : ItemSspBlockTextInputBinding, private val listener: SspBlockTextInputListner) : ViewHolder(binding.root) {
        override fun bind(item: BlockWrapper, position: Int) {
            binding.blockWrapper = item
            binding.listener = listener
        }
    }

    interface SspBlockDateFieldListener {
        fun onDateBlockClick(blockWrapper: BlockWrapper)
    }

    interface SspBlockSelectClickListener {
        fun onOptionSelected(blockWrapper: BlockWrapper, option: SelectionOption)
    }

    interface SspBlockTextInputListner {
        fun onTextInputChanged(blockWrapper: BlockWrapper, text: String)
    }
}

object BlockWrapperItemDiff : DiffUtil.ItemCallback<BlockWrapper>() {
    override fun areItemsTheSame(p0: BlockWrapper, p1: BlockWrapper): Boolean = p0.pageBuildingBlock.id == p1.pageBuildingBlock.id
    override fun areContentsTheSame(p0: BlockWrapper, p1: BlockWrapper): Boolean = p0 == p1
}
```

### 3. List blocks

```kotlin
private lateinit var blockAdapter: SspActBlockAdapter
private var pageBuildingRecycler: RecyclerView? = null
	
pageBuildingRecycler = binding.pageBuildingRecycler
binding.pageBuildingRecycler.adapter = blockAdapter

pageBuildingRecycler?.setItemViewCacheSize(pageBuildingBlocks.size)
blockAdapter.submitList(pageBuildingBlocks)
```

In your fragment or activity class submit list of blocks to the adapter:

### 4. Create layouts to present different types of blocks:


```kotlin
// Date field
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    <data>
        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />

        <variable
            name="listener"
            type="com.rezolve.demo.content.sspact.SspActBlockAdapter.SspBlockDateFieldListener" />
    </data>
    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="@{() -> listener.onDateBlockClick(blockWrapper)}"
        android:layout_margin="@dimen/ssp_block_paragraph_padding">
        <androidx.appcompat.widget.AppCompatTextView
            android:id="@+id/ssp_block_date_button"
            style="@style/SspBlockBaseStyle.Title"
            android:text="@{blockWrapper.pageBuildingBlock.data.text.concat(blockWrapper.pageBuildingBlock.required ? @string/ssp_block_required_suffix : @string/empty_string)}"
            android:textColor="@{blockWrapper.pageBuildingBlock.required &amp;&amp; blockWrapper.answerToDisplay == null ? @color/ssp_block_field_required : @color/black}"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            tools:text="When would you like to visit us?"/>
        <androidx.appcompat.widget.AppCompatTextView
            android:id="@+id/ssp_block_date_answer_display"
            style="@style/SspBlockBaseStyle.Answer"
            android:text="@{blockWrapper.answerToDisplay == null ? @string/ssp_block_select : blockWrapper.answerToDisplay}"
            app:layout_constraintTop_toBottomOf="@id/ssp_block_date_button"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            tools:text="12-04-2020"/>
        <androidx.appcompat.widget.AppCompatImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintTop_toTopOf="@id/ssp_block_date_answer_display"
            app:layout_constraintBottom_toBottomOf="@id/ssp_block_date_answer_display"
            app:layout_constraintEnd_toEndOf="parent"
            android:layout_marginEnd="@dimen/ssp_block_paragraph_padding_small"
            android:src="@drawable/ic_arrow_drop_down_black_24dp"/>

    </androidx.constraintlayout.widget.ConstraintLayout>

</layout>


// Divider 
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <View
        android:layout_width="match_parent"
        android:layout_height="2dp"
        android:layout_margin="@dimen/ssp_block_paragraph_padding"
        android:background="@color/ssp_block_light_grey"/>
</layout>


// Header
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />
    </data>
    <androidx.appcompat.widget.AppCompatTextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="@dimen/ssp_block_header_text_size"
        android:paddingTop="10dp"
        android:paddingBottom="10dp"
        android:paddingStart="@dimen/ssp_block_paragraph_padding"
        android:paddingEnd="@dimen/ssp_block_paragraph_padding"
        android:layout_marginTop="4dp"
        android:layout_marginBottom="4dp"
        blockText="@{blockWrapper.pageBuildingBlock}"/>
</layout>


// Image 
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />
    </data>
    <androidx.appcompat.widget.AppCompatImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:contentDescription="@{blockWrapper.pageBuildingBlock.data.text}"
        android:adjustViewBounds="true"
        android:layout_marginBottom="3dp"
        imageUrl="@{blockWrapper.pageBuildingBlock.data.url}"/>
</layout>


// Paragraph
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />
    </data>
    <androidx.appcompat.widget.AppCompatTextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="@dimen/ssp_block_standard_text_size"
        android:padding="@dimen/ssp_block_paragraph_padding"
        blockText="@{blockWrapper.pageBuildingBlock}"/>
</layout>


// Select box
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    <data>
        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />
        <variable
            name="listener"
            type="com.rezolve.demo.content.sspact.SspActBlockAdapter.SspBlockSelectClickListener" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="@dimen/ssp_block_paragraph_padding">
        <androidx.appcompat.widget.AppCompatTextView
            android:id="@+id/ssp_block_select_label"
            style="@style/SspBlockBaseStyle.Title"
            android:text="@{blockWrapper.pageBuildingBlock.data.text.concat(blockWrapper.pageBuildingBlock.required ? @string/ssp_block_required_suffix : @string/empty_string)}"
            android:textColor="@{blockWrapper.pageBuildingBlock.required &amp;&amp; blockWrapper.answerToDisplay == null ? @color/ssp_block_field_required : @color/black}"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            tools:text="Pick a color"/>
        <androidx.appcompat.widget.AppCompatTextView
            android:id="@+id/ssp_block_select_button"
            style="@style/SspBlockBaseStyle.Answer"
            android:text="@{blockWrapper.answerToDisplay == null ? @string/ssp_block_select : blockWrapper.answerToDisplay}"
            app:layout_constraintTop_toBottomOf="@id/ssp_block_select_label"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            android:onClick="@{(v) -> sspBlockSelectLayout.toggle()}"
            tools:text="Select"/>
        <androidx.appcompat.widget.AppCompatImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintTop_toTopOf="@id/ssp_block_select_button"
            app:layout_constraintBottom_toBottomOf="@id/ssp_block_select_button"
            app:layout_constraintEnd_toEndOf="parent"
            android:layout_marginEnd="@dimen/ssp_block_paragraph_padding_small"
            android:src="@drawable/ic_arrow_drop_down_black_24dp"/>
        <net.cachapa.expandablelayout.ExpandableLayout
            android:id="@+id/ssp_block_select_layout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:el_duration="300"
            app:el_expanded="false"
            app:el_parallax="1"
            app:layout_constraintTop_toBottomOf="@id/ssp_block_select_button">
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical"
                selectItems="@{blockWrapper}"
                selectListener="@{listener}"/>
        </net.cachapa.expandablelayout.ExpandableLayout>
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>



// Text input
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <import type="android.text.TextUtils"/>

        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />

        <variable
            name="listener"
            type="com.rezolve.demo.content.sspact.SspActBlockAdapter.SspBlockTextInputListner" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="@dimen/ssp_block_paragraph_padding">

        <androidx.appcompat.widget.AppCompatTextView
            android:id="@+id/ssp_block_text_input_label"
            style="@style/SspBlockBaseStyle.Title"
            android:text="@{blockWrapper.pageBuildingBlock.data.text.concat(blockWrapper.pageBuildingBlock.required ? @string/ssp_block_required_suffix : @string/empty_string)}"
            android:textColor="@{blockWrapper.pageBuildingBlock.required &amp;&amp; TextUtils.isEmpty(blockWrapper.answerToDisplay) ? @color/ssp_block_field_required : @color/black}"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            tools:text="Special requests"/>

        <androidx.appcompat.widget.AppCompatEditText
            android:id="@+id/ssp_block_text_input_edittext"
            style="@style/SspBlockBaseStyle.Answer"
            label="@{sspBlockTextInputLabel}"
            block="@{blockWrapper}"
            textListener="@{listener}"
            app:layout_constraintTop_toBottomOf="@id/ssp_block_text_input_label"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"/>

    </androidx.constraintlayout.widget.ConstraintLayout>

</layout>


// Video
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="blockWrapper"
            type="com.rezolve.demo.content.sspact.BlockWrapper" />
    </data>
    <WebView
        android:layout_width="match_parent"
        android:layout_height="240dp"
        videoUrl="@{blockWrapper.pageBuildingBlock.data.url}"/>
</layout>

```
Create layouts to present different types of blocks. An example of each type is provided to the right. 


### 5. Create helper extension methods

```kotlin
@BindingAdapter("blockText")
fun setBlockText(view: AppCompatTextView, value: PageBuildingBlock) {
    view.apply {
        text = value.data.text

        value.style?.let {
            setTextColor(Color.parseColor(it.color))
            setBackgroundColor(Color.parseColor(it.backgroundColor))
            val typeFace = when {
                it.fontWeight == FontWeight.BOLD && it.fontStyle == FontStyle.ITALIC -> Typeface.BOLD_ITALIC
                it.fontWeight == FontWeight.BOLD -> Typeface.BOLD
                it.fontStyle == FontStyle.ITALIC -> Typeface.ITALIC
                else -> Typeface.NORMAL
            }
            setTypeface(null, typeFace)
            gravity = when(it.textAlign) {
                TextAlign.CENTER -> Gravity.CENTER
                TextAlign.RIGHT -> Gravity.RIGHT
                else -> Gravity.LEFT
            }
        }
    }
}

@BindingAdapter("block", "textListener", "label")
fun setTextListener(view: AppCompatEditText, blockWrapper: BlockWrapper, listener: SspActBlockAdapter.SspBlockTextInputListner, labelView: AppCompatTextView) {
    view.addTextChangedListener(object : TextWatcher {
        override fun beforeTextChanged(p0: CharSequence?, p1: Int, p2: Int, p3: Int) { }
        override fun onTextChanged(text: CharSequence?, p1: Int, p2: Int, p3: Int) {
            listener.onTextInputChanged(blockWrapper, text.toString())
            labelView.setTextColor(ContextCompat.getColor(view.context, if (blockWrapper.pageBuildingBlock.isRequired && text.isNullOrEmpty()) R.color.ssp_block_field_required else R.color.black))
        }
        override fun afterTextChanged(p0: Editable?) { }
    })
}

@BindingAdapter("imageUrl")
fun setImageUrl(view: AppCompatImageView, url: String?) {
    if (!url.isNullOrEmpty()) {
        Picasso.get().load(url).into(view)
    }
}

@SuppressLint("SetJavaScriptEnabled")
@BindingAdapter("videoUrl")
fun setVideoUrl(view: WebView, url: String) {
    view.settings.apply {
        javaScriptEnabled = true
        loadWithOverviewMode = true
        useWideViewPort = true
    }
    view.loadUrl(url)
}

@BindingAdapter("selectItems", "selectListener")
fun setSelectItems(view: LinearLayout, blockWrapper: BlockWrapper, listener: SspActBlockAdapter.SspBlockSelectClickListener) {
    view.removeAllViews()
    blockWrapper.pageBuildingBlock.data.selectionOptions?.forEach {
        val textView = AppCompatTextView(view.context).apply {
            layoutParams = LinearLayout.LayoutParams(
                    LinearLayout.LayoutParams.MATCH_PARENT,
                    LinearLayout.LayoutParams.WRAP_CONTENT)
            setPadding(20, 12, 0, 12)
            text = it.description
            setTextColor(ContextCompat.getColor(view.context, R.color.almost_black))
            setOnClickListener { _ ->
                (view.parent as ExpandableLayout).collapse()
                listener.onOptionSelected(blockWrapper, it)
            }
        }
        view.addView(textView)
    }
}
```




### 6. Submitting Act answers

SspAct has fields called questions:

`private List<SspActQuestion> questions;`

If it's not null or empty it means that the merchant would like to collect some informations from users. If `SspActQuestion.isRequired()` field is `true` it means that answer submission won't be possible without it.

There are three types of `SspActQuestion`, and fields answers must pass validation otherwise `IllegalArgumentException` will be thrown.:

* `SspActQuestionDate` - `userAnswer` must be in `dd/mm/yyyy hh:mm` format.
* `SspActQuestionDropdown` - `userAnswer` must equal id of one of provided `SspActQuestionValue(s)`.
* `SspActQuestionField` - no validation

You can create an answer for each of these classes using their `answer(@NonNull String userAnswer)` method. 




### 6a. Build the Act submission

```kotlin
private fun assembleSspActSubmission(): SspActSubmission {
	return SspActSubmission.Builder()
		.setUserId(userId)
		.setUserName(userName)
		.setPhone(userPhone)
		.setPersonTitle(userTitle)
		.setFirstName(userFirstName)
		.setLastName(userLastName)
		.setEmail(userEmail)
		.setServiceId(sspAct.serviceId)
		.setAnswers(listOfSspActAnswers)
		.setLocation(rezolveLocation)
		.build()
}
```

When you have `var listOfSspActAnswers: List<SspActAnswer>` you can build `SspActSubmission` class using the builder tool:

### 6b. Submit the Act answers

```kotlin
sspActManager.submitAnswer(
	sspAct.id,
	assembleSspActSubmission(),
	object : SspSubmitActDataInterface {
		override fun onSubmitActDataSuccess(actSubmissionResponse: SspActSubmissionResponse) {
			// handle response
		}
		override fun onError(error: RezolveError) {
			// handle error
		}
	}
)
```

Next, you can submit your answer:

## Look Up An Act By Id - Android

```kotlin
SspActManager.getResolveResponseFromEngagementId(int imageWidth,
       @NonNull String engagementId,
       @NonNull final SspFromEngagementInterface sspFromEngagementInterface)
```

If you have an Act Id, you can retrieve it using the example code to the right. 

