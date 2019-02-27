ListView的重绘是导致EditText失去焦点的原因。如果想要获取焦点，可以定义一个变量记录用户点击的位置，然后在getView()中根据当前位置与所记录位置是否相等来进行手动的获取焦点和清除焦点的操作。实现代码如下:

```
private int mTouchItemPosition = -1;
 public View getView(final int position, View contentView, ViewGroup viewGroup) {
        ViewHolder viewHolder = null;
        if (contentView == null) {
            viewHolder = new ViewHolder();
            contentView = mInflater.inflate(R.layout.list_item_score, null);
            viewHolder.score = (EditText) contentView.findViewById(R.id.tv_score);
            viewHolder.score.setOnTouchListener(new View.OnTouchListener() {
                @Override
                public boolean onTouch(View view, MotionEvent motionEvent) {
                    ((ViewGroup) view.getParent())
                            .setDescendantFocusability(ViewGroup.FOCUS_AFTER_DESCENDANTS);
                    mTouchItemPosition = (Integer) view.getTag();
           
                    return false;
                }
            });
            contentView.setTag(viewHolder);
 
        } else {
            viewHolder = (ViewHolder) contentView.getTag();
        }
        viewHolder.score.setTag(position);
        if (mTouchItemPosition != -1 && mTouchItemPosition == position) {
            viewHolder.score.requestFocus();
            viewHolder.score.setSelection(viewHolder.score.getText().length());
        } else {
            viewHolder.score.clearFocus();
        }
        viewHolder.score.setText(mList.get(position).getId());
        return contentView;
    }
```