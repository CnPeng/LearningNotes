```
 tv_maxLines.viewTreeObserver.addOnPreDrawListener(object : ViewTreeObserver.OnPreDrawListener {
            override fun onPreDraw(): Boolean {
                val lineCount = tv_maxLines.lineCount
                val lineLimit = tv_maxLines.maxLines

                if (lineCount > lineLimit) {
                    //获取限定行数末尾的字符索引
                    val limitLastStrIndex = tv_maxLines.layout.getLineEnd(lineLimit);
                    val subsStr = tv_maxLines.editableText.subSequence(0, limitLastStrIndex - 3).toString()
                    val strToShow = subsStr.plus("...")
                    tv_maxLines.text = strToShow

                }

                //使用完毕记得移除
                tv_maxLines.viewTreeObserver.removeOnPreDrawListener(this)
                return true
            }
        })
```