带Check状态的ImageView

<https://blog.csdn.net/cjluseacher/article/details/49149059>

```
import android.content.Context;
import android.support.annotation.Nullable;
import android.util.AttributeSet;
import android.widget.Checkable;
import android.widget.ImageView;

public class CheckImageView extends ImageView implements Checkable {
    private boolean mChecked;
    // 增加需要支持新的flag
    private static final int[] CHECKED_STATE_SET = {android.R.attr.state_checked};

    public CheckImageView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public int[] onCreateDrawableState(final int extraSpace) {
        // 复写 onCreateDrawableState, 并增加给出新的flag位置, 并根据当前状态，merge进相应的flag
        final int[] drawableState = super.onCreateDrawableState(extraSpace + 1);
        if (isChecked())
            mergeDrawableStates(drawableState, CHECKED_STATE_SET);
        return drawableState;
    }

    @Override
    public void setChecked(boolean checked) {
        if (mChecked != checked) {
            mChecked = checked;
            refreshDrawableState();
        }
    }

    @Override
    public boolean isChecked() {
        return mChecked;
    }

    @Override
    public void toggle() {
        setChecked(!mChecked);
    }
}
```