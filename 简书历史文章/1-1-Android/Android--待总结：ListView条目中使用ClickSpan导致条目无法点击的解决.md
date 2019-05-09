条目中包含TextView，TextView中的文本使用了SpannableString，而SpannableString又设置了ClickSpan,要想这个点击事件生效，就需要设置 TextView.setMovementMethod( ) ， 但是设置之后，条目的点击事件不能响应了，解决方案是使用数据绑定，在布局文件中写入点击事件。

这样两者都能实现点击了。

-- 思考：重点的问题应该在于不是在xml 中还是 .java 中写点击事件，而在于是否给TextView有设置点击监听！！！！？？？？
