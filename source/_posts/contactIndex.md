title: Android通讯录索引效果
date: 2015-08-29 11:04:21
tags: contact
category: Android
---
> 通讯录索引效果，网上很多了，我这里做个记录。

# 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/contactIndex.png)
<!--more-->
# SideBar
此类自定义索引的字母
```java
public class SideBar extends View {

    // 触摸事件
    private OnTouchingLetterChangedListener onTouchingLetterChangedListener;
    // 26个字母
    public static String[] A_Z = {"A", "B", "C", "D", "E", "F", "G", "H", "I",
            "J", "K", "L", "M", "N", "O", "P", "Q", "R", "S", "T", "U", "V",
            "W", "X", "Y", "Z", "#"};
    private int choose = -1;// 选中
    private Paint paint = new Paint();

    private TextView mTextDialog;

    /**
     * 为SideBar设置显示字母的TextView
     *
     * @param mTextDialog
     */
    public void setTextView(TextView mTextDialog) {
        this.mTextDialog = mTextDialog;
    }


    public SideBar(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
    }

    public SideBar(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public SideBar(Context context) {
        super(context);
    }

    /**
     * 重写这个方法
     */
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        // 获取焦点改变背景颜色.
        int height = getHeight();// 获取对应高度
        int width = getWidth(); // 获取对应宽度
        int singleHeight = height / A_Z.length - 2;// 获取每一个字母的高度  (这里-2仅仅是为了好看而已)

        for (int i = 0; i < A_Z.length; i++) {
            paint.setColor(Color.rgb(33, 65, 98));  //设置字体颜色
            paint.setTypeface(Typeface.DEFAULT_BOLD);  //设置字体
            paint.setAntiAlias(true);  //设置抗锯齿
            paint.setTextSize(30);  //设置字母字体大小
            // 选中的状态
            if (i == choose) {
                paint.setColor(Color.parseColor("#3399ff"));  //选中的字母改变颜色
                paint.setFakeBoldText(true);  //设置字体为粗体
            }
            // x坐标等于中间-字符串宽度的一半.
            float xPos = width / 2 - paint.measureText(A_Z[i]) / 2;
            float yPos = singleHeight * i + singleHeight;
            canvas.drawText(A_Z[i], xPos, yPos, paint);  //绘制所有的字母
            paint.reset();// 重置画笔
        }

    }

    @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        final int action = event.getAction();
        final float y = event.getY();// 点击y坐标
        final int oldChoose = choose;
        final OnTouchingLetterChangedListener listener = onTouchingLetterChangedListener;
        final int c = (int) (y / getHeight() * A_Z.length);// 点击y坐标所占总高度的比例*b数组的长度就等于点击b中的个数.

        switch (action) {
            case MotionEvent.ACTION_UP:
                setBackgroundDrawable(new ColorDrawable(0x00000000));
                choose = -1;//
                invalidate();
                if (mTextDialog != null) {
                    mTextDialog.setVisibility(View.INVISIBLE);
                }
                break;

            default:
                setBackgroundResource(R.color.colorPrimary);
                if (oldChoose != c) {  //判断选中字母是否发生改变
                    if (c >= 0 && c < A_Z.length) {
                        if (listener != null) {
                            listener.onTouchingLetterChanged(A_Z[c]);
                        }
                        if (mTextDialog != null) {
                            mTextDialog.setText(A_Z[c]);
                            mTextDialog.setVisibility(View.VISIBLE);
                        }

                        choose = c;
                        invalidate();
                    }
                }

                break;
        }
        return true;
    }

    /**
     * 向外公开的方法
     *
     * @param onTouchingLetterChangedListener
     */
    public void setOnTouchingLetterChangedListener(
            OnTouchingLetterChangedListener onTouchingLetterChangedListener) {
        this.onTouchingLetterChangedListener = onTouchingLetterChangedListener;
    }

    /**
     * 接口
     *
     * @author coder
     */
    public interface OnTouchingLetterChangedListener {
        public void onTouchingLetterChanged(String s);
    }
}
```

# xml调用
```js
 <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.RecyclerView
            android:id="@+id/mRecyclerView"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />

        <TextView
            android:id="@+id/textDialog"
            android:layout_width="80dip"
            android:layout_height="80dip"
            android:layout_centerInParent="true"
            android:layout_gravity="center"
            android:gravity="center"
            android:textColor="#ffffffff"
            android:background="@color/colorPrimary"
            android:textSize="30.0dip"
            android:visibility="invisible" />

        <你的包名.SideBar
            android:id="@+id/sidebar"
            android:layout_width="20dip"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true" />

    </RelativeLayout>
```
# ContactActivity
```java
public class ContactActivity extends BaseActivity {
    @Bind(R.id.mRecyclerView)
    RecyclerView mRecyclerView;

    @Bind(R.id.sidebar)
    SideBar sidebar;
    @Bind(R.id.textDialog)
    TextView textDialog;
    private List<PersonBean> data;
    ContactAdapter mContactAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_contact);        
        sidebar.setTextView(textDialog);
        // 设置字母导航触摸监听
        sidebar.setOnTouchingLetterChangedListener(new SideBar.OnTouchingLetterChangedListener() {

            @Override
            public void onTouchingLetterChanged(String s) {
                // TODO Auto-generated method stub
                // 该字母首次出现的位置
                int position = mContactAdapter.getPositionForSelection(s.charAt(0));

                if (position != -1) {
                    mRecyclerView.getLayoutManager().scrollToPosition(position);
                }
            }
        });
        data = getData(getResources().getStringArray(R.array.listpersons));
        // 数据在放在adapter之前需要排序
        Collections.sort(data, new PinyinComparator());
        for (int i = 0; i < data.size(); i++) {
            Log.i("wxl", "Fpinyin=" + data.get(i).getFirstPinYin());
        }
        LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this);
        linearLayoutManager.setOrientation(LinearLayoutManager.VERTICAL);
        mRecyclerView.setLayoutManager(linearLayoutManager);
        mContactAdapter = new ContactAdapter(data);
        mRecyclerView.setAdapter(mContactAdapter);
    }

    private List<PersonBean> getData(String[] data) {
        List<PersonBean> listarray = new ArrayList<PersonBean>();
        for (int i = 0; i < data.length; i++) {
            String pinyin = PinyinUtils.getPingYin(data[i]);
            String Fpinyin = pinyin.substring(0, 1).toUpperCase();

            PersonBean person = new PersonBean();
            person.setName(data[i]);
            person.setPinYin(pinyin);
            // 正则表达式，判断首字母是否是英文字母
            if (Fpinyin.matches("[A-Z]")) {
                person.setFirstPinYin(Fpinyin);
            } else {
                person.setFirstPinYin("#");
            }

            listarray.add(person);
        }

        return listarray;

    }  
   
}
```
# persons.xml
模拟数据
```js 
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="listpersons">
        <item> 马云</item>
        <item> 马化腾</item>
        <item> 佩兰</item>
        <item> 奥巴马</item>
        <item> 普京</item>
        <item> 金三胖</item>
        <item> 暗影军团</item>
        <item> 141特遣队</item>
        <item> 冲锋号</item>
        <item> +胜利号</item>
        <item> 超级赛亚人</item>
        <item> 金克拉</item>
        <item> 亚古兽</item>
        <item> 数码宝贝大冒险</item>
        <item> 大胜归来</item>
        <item> 道士下山</item>
        <item> A利亚克</item>
        <item> 胜利冲锋龙卷风</item>
        <item> 走在冷风中</item>
        <item> 外面的世界</item>
        <item> 烦恼歌</item>
        <item> 如果没有你</item>
        <item> 四季歌</item>
        <item> 南山南</item>
        <item> +胜利号</item>
        <item> 光能使者</item>
        <item> 铁甲小宝</item>
        <item> 皮卡丘</item>
        <item> 奥特曼</item>
        <item> 开普勒452b</item>
        <item> 神舟20号</item>
        <item> 屌炸天的结尾</item>
    </string-array>
</resources>

```

# PersonBean
```java
public class PersonBean {

    private String Name;
    private String PinYin;
    private String FirstPinYin;

    public String getName() {
        return Name;
    }

    public void setName(String name) {
        Name = name;
    }

    public String getPinYin() {
        return PinYin;
    }

    public void setPinYin(String pinYin) {
        PinYin = pinYin;
    }

    public String getFirstPinYin() {
        return FirstPinYin;
    }

    public void setFirstPinYin(String firstPinYin) {
        FirstPinYin = firstPinYin;
    }

    public String toString() {
        return "姓名：" + getName() + "   拼音：" + getPinYin() + "    首字母："
                + getFirstPinYin();

    }
}
```
# PinyinUtils
这里需要pinyin4j包，下载地址：http://pinyin4j.sourceforge.net/
```java
public class PinyinUtils {
    /**
     * 汉字转拼音
     */
    public static String getPingYin(String inputString) {
        HanyuPinyinOutputFormat format = new HanyuPinyinOutputFormat();
        format.setCaseType(HanyuPinyinCaseType.LOWERCASE);
        format.setToneType(HanyuPinyinToneType.WITHOUT_TONE);
        format.setVCharType(HanyuPinyinVCharType.WITH_V);

        char[] input = inputString.trim().toCharArray();
        String output = "";

        try {
            for (char curchar : input) {
                if (java.lang.Character.toString(curchar).matches("[\\u4E00-\\u9FA5]+")) {
                    String[] temp = PinyinHelper.toHanyuPinyinStringArray(curchar, format);
                    output += temp[0];
                } else
                    output += java.lang.Character.toString(curchar);
            }
        } catch (BadHanyuPinyinOutputFormatCombination e) {
            e.printStackTrace();
        }
        return output;
    }
}
```
# PinyinComparator
按照字母排序类
```java
public class PinyinComparator implements Comparator<PersonBean> {

    @Override
    public int compare(PersonBean lhs, PersonBean rhs) {
        return sort(lhs, rhs);
    }

    private int sort(PersonBean lhs, PersonBean rhs) {
        // 获取ascii值
        int lhs_ascii = lhs.getFirstPinYin().toUpperCase().charAt(0);
        int rhs_ascii = rhs.getFirstPinYin().toUpperCase().charAt(0);
        // 判断若不是字母，则排在字母之后
        if (lhs_ascii < 65 || lhs_ascii > 90)
            return 1;
        else if (rhs_ascii < 65 || rhs_ascii > 90)
            return -1;
        else
            return lhs.getPinYin().compareTo(rhs.getPinYin());
    }
}

```
# ContactAdapter
```java
public class ContactAdapter extends RecyclerView.Adapter<ContactAdapter.ViewHolder> {
    List<PersonBean> data;

    public ContactAdapter(List<PersonBean> data) {
        this.data = data;
    }

    class ViewHolder extends RecyclerView.ViewHolder {
        TextView nickname;
        TextView tag;

        public ViewHolder(View itemView) {
            super(itemView);
            nickname = (TextView) itemView.findViewById(R.id.nickname);
            tag = (TextView) itemView.findViewById(R.id.tag);
        }
    }


    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = View.inflate(parent.getContext(), R.layout.contact_item, null);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        PersonBean person = data.get(position);
        // 获取首字母的assii值
        int selection = person.getFirstPinYin().charAt(0);
        // 通过首字母的assii值来判断是否显示字母
        int positionForSelection = getPositionForSelection(selection);
        if (position == positionForSelection) {// 相等说明需要显示字母
            holder.tag.setVisibility(View.VISIBLE);
            holder.tag.setText(person.getFirstPinYin());
        } else {
            holder.tag.setVisibility(View.GONE);

        }
        holder.nickname.setText(data.get(position).getName());
    }

    public int getPositionForSelection(int selection) {
        for (int i = 0; i < data.size(); i++) {
            String Fpinyin = data.get(i).getFirstPinYin();

            char first = Fpinyin.toUpperCase().charAt(0);
            if (first == selection) {
                return i;
            }
        }
        return -1;

    }

    @Override
    public int getItemCount() {
        return data.size();
    }
}
```
## contact_item.xml
```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <!-- 这个TextView就是显示字母的 -->
    <TextView
        android:id="@+id/tag"
        android:layout_width="match_parent"
        android:layout_height="20dp"
        android:background="#e6e6e6"
        android:gravity="center_vertical"
        android:paddingLeft="10dip"
        android:text="Z"
        android:visibility="visible" />

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <View
            android:id="@+id/view_lv_item_line"
            android:layout_width="match_parent"
            android:layout_height="0.5dip"
            android:layout_marginLeft="10dip"
            android:layout_marginRight="20dip"
            android:background="#174465" />

        <ImageView
            android:id="@+id/iv_lv_item_head"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/view_lv_item_line"
            android:layout_marginLeft="5dp"
            android:src="@mipmap/ic_launcher" />

        <TextView
            android:id="@+id/nickname"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerVertical="true"
            android:layout_marginLeft="5dip"
            android:layout_toRightOf="@id/iv_lv_item_head"
            android:text="周华健" />
    </RelativeLayout>

</LinearLayout>
```
# 附录
[Android 联系人字母排序（仿微信）](http://www.cnblogs.com/scetopcsa/p/4724308.html)

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
