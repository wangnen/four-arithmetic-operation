# four-arithmetic-operation
具有以下功能：
（1）能根据题目回答情况，自动判别答案的正误，完成最后总成绩的统计、输出；
（2）题目不要出现重复；


扩展要求：
（3）可以控制下列参数：是否有乘除法、是否有括号、数值范围、加减法有无负数、除法有无余数、是否支持分数、是否支持小数；
（4）可定制打印间隔

高级要求：
（5）做成手机app应用程序；
### 解题思路描述

* 了解并确认需求
* 查阅资料，获取网上开源代码，对其进行修改复用
* 与同学，老师讨论程序疑难问题
* 对程序进行测试，修改
* 强大的`自定义`功能，方便定制自己的题目要求

### 设计实现过程

* 第一阶段任务：
 * 设置题目前置条件的选择；
 * 用户输入条件；
 * 程序做出应答，自动按要求出题；
 * 用户输入答案；
 * 程序判断输入答案正误；
 * 汇总正确题目个数及分数；
* 第二阶段任务：
 * 程序测试功能是否实现；
 * 依据扩展需求进行系统升级；
 * 开发用户UI界面，提升用户体验度；
 * 开发手机移动端系统；

### 部分代码
```
import android.content.DialogInterface;
import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.AppCompatCheckBox;
import android.text.InputType;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.SeekBar;

import java.util.ArrayList;
import java.util.List;

import butterknife.BindView;
import butterknife.ButterKnife;
import homework.calc.adapter.QAAdapter;
import homework.calc.dataType.Formula;
import homework.calc.decorator.BracketDecor;
import homework.calc.decorator.DecimalDecor;
import homework.calc.decorator.FractionDecor;
import homework.calc.decorator.MultiAndDivideDecor;
import homework.calc.decorator.NegativeDecor;
import homework.calc.decorator.RemainsDecor;

public class MainActivity extends AppCompatActivity implements View.OnClickListener, SeekBar.OnSeekBarChangeListener, AdapterView.OnItemClickListener {

    @BindView(R.id.check_box_mul_and_divide)
    AppCompatCheckBox checkBoxMulAndDivide;
    @BindView(R.id.check_box_bracket)
    AppCompatCheckBox checkBoxBracket;
    @BindView(R.id.check_box_negative)
    AppCompatCheckBox checkBoxNegative;
    @BindView(R.id.check_box_remains)
    AppCompatCheckBox checkBoxRemains;
    @BindView(R.id.check_box_fraction)
    AppCompatCheckBox checkBoxFraction;
    @BindView(R.id.check_box_decimal)
    AppCompatCheckBox checkBoxDecimal;
    @BindView(R.id.minimum_value)
    EditText minimumValue;
    @BindView(R.id.maximum_value)
    EditText maximumValue;
    @BindView(R.id.line_space)
    SeekBar lineSpace;
    @BindView(R.id.button_generate)
    Button buttonGenerate;
    @BindView(R.id.button_check)
    Button buttonCheck;
    @BindView(R.id.list_questions)
    ListView listQuestions;
    private QAAdapter adapter;
    private List<Formula> formulaList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
        buttonGenerate.setOnClickListener(this);
        buttonCheck.setOnClickListener(this);
        adapter = new QAAdapter(this, formulaList);
        listQuestions.setAdapter(adapter);
        listQuestions.setOnItemClickListener(this);
        lineSpace.setOnSeekBarChangeListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v == buttonGenerate) {
            formulaList.clear();
            for (int i = 0; i < 10; i++) {
                Formula formula = new Formula(
                        Integer.valueOf(minimumValue.getText().toString())
                        , Integer.valueOf(maximumValue.getText().toString()));
                if (checkBoxMulAndDivide.isChecked()) {
                    formula = new MultiAndDivideDecor(formula);
                }
                if (checkBoxBracket.isChecked()) {
                    formula = new BracketDecor(formula);
                }
                if (checkBoxNegative.isChecked()) {
                    formula = new NegativeDecor(formula);
                }
                if (checkBoxRemains.isChecked()) {
                    formula = new RemainsDecor(formula);
                }
                if (checkBoxFraction.isChecked()) {
                    formula = new FractionDecor(formula);
                }
                if (checkBoxDecimal.isChecked()) {
                    formula = new DecimalDecor(formula);
                }
                formulaList.add(formula);
            }
            adapter.notifyDataSetChanged();
        } else if (v == buttonCheck) {
            for (Formula f : formulaList) {
                f.correctAnswer = String.valueOf(f.evaluate());
            }
            adapter.notifyDataSetChanged();
        }
    }

    @Override
    public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
        listQuestions.setDividerHeight(progress);
    }

    @Override
    public void onStartTrackingTouch(SeekBar seekBar) {

    }

    @Override
    public void onStopTrackingTouch(SeekBar seekBar) {

    }

    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        final Formula f = formulaList.get(position);
        final EditText et = new EditText(this);
        et.setHint("两位小数");
        et.setInputType(InputType.TYPE_NUMBER_FLAG_DECIMAL);
        new AlertDialog.Builder(this).setTitle("输入答案")
                .setView(et).setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                String s = et.getText().toString();
                if (!s.equals("")) {
                    f.userAnswer = s;
                }
            }
        }).show();
        adapter.notifyDataSetChanged();
    }
}

```


### 测试
![](https://github.com/tyut-zhangzhiyuan/four/blob/master/test/测试1.png)

### PSP表格
![](https://github.com/tyut-zhangzhiyuan/four/blob/master/PSP.png)
