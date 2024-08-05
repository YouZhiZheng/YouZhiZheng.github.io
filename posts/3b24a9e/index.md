# IEDA点工具运行前环境变量设置

## 脚本创建
在单独运行点工具前必须配置对应的环境变量，在iEDA目录/iEDA/scripts/design/sky130_gcd下使用命令`touch`创建一个.sh脚本，内容如下:
```bash
#!/bin/bash
CURRENT_DIR=&#34;$(pwd)&#34;

export CONFIG_DIR=&#34;$CURRENT_DIR/iEDA_config&#34; 
export RESULT_DIR=&#34;$CURRENT_DIR/result/my_test_result&#34;
export TCL_SCRIPT_DIR=&#34;$CURRENT_DIR/script&#34;
export NETLIST_FILE=&#34;$CURRENT_DIR/result/verilog/gcd.v&#34;
export FOUNDRY_DIR=&#34;$CURRENT_DIR/../../foundry/sky130&#34;
export SPEF_FILE=&#34;$CURRENT_DIR/../../foundry/sky130/spef/gcd.spef&#34;
export SDC_FILE=&#34;$CURRENT_DIR/../../foundry/sky130/sdc/gcd.sdc&#34;
export DESIGN_TOP=&#34;gcd&#34;
export DIE_AREA=&#34;0.0   0.0   149.96   150.128&#34;
export CORE_AREA=&#34;9.996 10.08 139.964  140.048&#34;

echo &#34;CONFIG_DIR set to: $CONFIG_DIR&#34;
echo &#34;RESULT_DIR set to: $RESULT_DIR&#34;
echo &#34;TCL_SCRIPT_DIR set to: $TCL_SCRIPT_DIR&#34;
echo &#34;NETLIST_FILE set to: $NETLIST_FILE&#34;
echo &#34;FOUNDRY_DIR set to: $FOUNDRY_DIR&#34;
echo &#34;SPEF_FILE set to: $SPEF_FILE&#34;
echo &#34;SDC_FILE set to: $SDC_FILE&#34;
echo &#34;DESIGN_TOP set to: $DESIGN_TOP&#34;
echo &#34;DIE_AREA set to: $DIE_AREA&#34;
echo &#34;CORE_AREA set to: $CORE_AREA&#34;
```

## 点工具运行
运行点工具前，在当前窗口使用命令 `source 脚本名.sh` 来设置环境变量，然后按照iEDA的用户手册来运行点工具即可

**PS：** 设置的环境变量只在当前窗口有效，即在新窗口运行点工具前需要再次运行脚本来设置环境变量，如果想要使得变量在全部窗口有效请自行谷歌。

## 参考资料
1. https://github.com/OSCC-Project/iEDA/blob/master/docs/user_guide/iEDA_user_guide.md

---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/3b24a9e/  

