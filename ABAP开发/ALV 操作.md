# ALV 操作

## 面向过程的ALV

### ALV显示函数 -1- *REUSE_ALV_GRID_DISPALY*
```ABAP
 TYPE-POOLS: SLIS.
```
#### 函数原型：
```ABAP
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
 EXPORTING
   I_INTERFACE_CHECK                 = ' '
   I_BYPASSING_BUFFER                = ' '
   I_BUFFER_ACTIVE                   = ' '
   I_CALLBACK_PROGRAM                = ' '  "指定回调程序-当前程序
   I_CALLBACK_PF_STATUS_SET          = ' '  "指定回调程序-设置GUI状态(子)程序
   I_CALLBACK_USER_COMMAND           = ' '  "指定回调程序-设置用户命令处理程序
   I_CALLBACK_TOP_OF_PAGE            = ' '
   I_CALLBACK_HTML_TOP_OF_PAGE       = ' '
   I_CALLBACK_HTML_END_OF_LIST       = ' '
   I_STRUCTURE_NAME                  =      "输出数据参考的数据字典的结构名,例如'SFLIGHT'.如果指定了这个参数,字段目录会自动生成,下面的参数IT_FIELDCATALOG不需要传值."
   I_BACKGROUND_ID                   = ' '
   I_GRID_TITLE                      =
   I_GRID_SETTINGS                   =
   IS_LAYOUT                         =      "ALV 布局参数设置"
   IT_FIELDCAT                       =      "ALV 列字段属性设置"
   IT_EXCLUDING                      =
   IT_SPECIAL_GROUPS                 =
   IT_SORT                           =      "排序的标准
   IT_FILTER                         =      "过滤的标准
   IS_SEL_HIDE                       =
   I_DEFAULT                         = 'X'  "决定用户是否可以定义默认的布局 ,'X' 可以定义默认布局,这个参数是默认的, SPACE 不可以定义默认布局
   I_SAVE                            = ' '  "决定用户是否可以保存变式 'X' 只能保存全局变式 ,'U' 只能保存特定变式,'A' 都可以保存,SPACE 不可以保存变式
   IS_VARIANT                        =      "决定布局显示的变式
   IT_EVENTS                         =
   IT_EVENT_EXIT                     =
   IS_PRINT                          =
   IS_REPREP_ID                      =
   I_SCREEN_START_COLUMN             = 0
   I_SCREEN_START_LINE               = 0
   I_SCREEN_END_COLUMN               = 0
   I_SCREEN_END_LINE                 = 0
   I_HTML_HEIGHT_TOP                 = 0
   I_HTML_HEIGHT_END                 = 0
   IT_ALV_GRAPHICS                   =
   IT_HYPERLINK                      =
   IT_ADD_FIELDCAT                   =
   IT_EXCEPT_QINFO                   =
   IR_SALV_FULLSCREEN_ADAPTER        =
 IMPORTING
   E_EXIT_CAUSED_BY_CALLER           =
   ES_EXIT_CAUSED_BY_USER            =
  TABLES
    T_OUTTAB                          =    "存放显示数据的内表"
 EXCEPTIONS
   PROGRAM_ERROR                     = 1
   OTHERS                            = 2
          .
```
#### 设置GRID属性
```ABAP
DATA: GS_LAYOUT TYPE SLIS_LAYOUT_ALV.
...
FORM FILL_GRID_CATA.
   GS_LATOUT-COLWIDTH_OPTIMIZE = 'X'. "优化列宽

   GS_LATOUT-ZEBRA             = 'X'. "行斑马显示

   GS_LATOUT-BOX_FIELDNAME     = 'SEL_FNAME'. "内表作选择标识字段

   GS_LAYOUT-COLOR_FIELDNAME   = 'COLOR_FNAME'. "内表作颜色标识字段

   GS_LAYOUT-NO_VLINE          = 'X' . "无列间分割线

   GS_LAYOUT-F2CODE            = '&ETA'. "设置触发弹出详细信息窗口的功能码，这里是双击

   ...
ENDFORM.
```
```ABAP
 ...
 IS_LAYOUT = GS_LAYOUT
```
#### 设置ALV列属性
```ABAP
DATA: GT_FIELDCAT TYPE SLIS_T_FIELDCAT_ALV,
      GS_FIELDCAT TYPE SLIS_FIELDCAT_ALV.
      ...
FORM FILL_FCAT.
  DEFINE DF_FILL_FCAT.
      GS_FIELDCAT-FIELDNAME    = &1.  "内表字段名
      GS_FIELDCAT-REPTEXT_DDIC = &2.  "列名文本
      "或使用seltext_s/m/l
      GS_FIELDCAT-JUST         = &3.  "对齐方式 L C R
      GS_FIELDCAT-ICON         = &4.  "SPACE X,X作为图标输出
      GS_FIELDCAT-CHECKBOX     = &5.  "SPACE X,X作为复选框输出
      GS_FIELDCAT-NO_ZERO      = &6.  "SPACE X,X为隐藏前导零 
      GS_FIELDCAT-HOTPOST      = &7.  "SPACE X,X作为热点显示 可触发用户命令事件
      ...
     APPEND GS_FIELDCAT TO GT_FIELDCAT.
     CLEAR GS_FIELDCAT.
  END-of-DEFINiTION.
  DF_FILL_FCAT 'CARRID' '承运人ID' 'L' '' '' 'X' .
  ...
ENDFORM.
```
```ABAP
...
IT_FIELDCAT = GT_FIELDCAT
```


#### 设置GUI状态
```ABAP
  ...
  FORM SET_PF_STATUS.

   DATA: LT_EXTAB TYPE SLIS_T_EXTAB,
         LS_EXTAB TYPE SLIS_EXTAB.

   LS_EXTAB-FCODE = 'DEMOB'.
   APPEND LS_EXTAB TO LT_EXTAB.

   "设置GUI Status并排除‘DEMOB’按钮功能码
   SET PF-STATUS 'Status name' EXCLUDING LT_EXTAB.

  ENDFORM.

```
```ABAP
...
I_CALLBACK_PF_STATUS_SET  = 'SET_PF_STATUS'
```


#### 设置用户功能码响应（双击/热点单击）
```ABAP
FORM USER_COMMAND USING F_UCOMM LIKE SY_UCOMM
                        FS_SELFIELD TYPE SLIS_SELFIELD.
  CASE F_UCOMM.
   WHEN '&IC1'. "点击事件
      READ TABLE GT_ITAB INTO GS_ITAB INDEX FS_SELFIELD-TABINDEX.
      IF FS_SELFIELD-FIELDNAME = 'CARRID'.点击热点字段响应
            ...
      ...
   WHEN '..'.
     ...
   OTHERS.
     ...
  ENDCASE.
ENDFORM.
```
```ABAP
...
I_CALLBACK_USER_COMMAND = 'USER_COMMAND '
```

###  ALV显示函数 -2- *REUSE_ALV_GRID_DISPALY_LVC*
#### 函数原型：
```ABAP
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
 EXPORTING
   I_INTERFACE_CHECK                 = ' '
   I_BYPASSING_BUFFER                =
   I_BUFFER_ACTIVE                   =
   I_CALLBACK_PROGRAM                = ' '
   I_CALLBACK_PF_STATUS_SET          = ' '
   I_CALLBACK_USER_COMMAND           = ' '
   I_CALLBACK_TOP_OF_PAGE            = ' '
   I_CALLBACK_HTML_TOP_OF_PAGE       = ' '
   I_CALLBACK_HTML_END_OF_LIST       = ' '
   I_STRUCTURE_NAME                  =
   I_BACKGROUND_ID                   = ' '
   I_GRID_TITLE                      =
   I_GRID_SETTINGS                   =
   IS_LAYOUT_LVC                     =
   IT_FIELDCAT_LVC                   =
   IT_EXCLUDING                      =
   IT_SPECIAL_GROUPS_LVC             =
   IT_SORT_LVC                       =
   IT_FILTER_LVC                     =
   IT_HYPERLINK                      =
   IS_SEL_HIDE                       =
   I_DEFAULT                         = 'X'
   I_SAVE                            = ' '
   IS_VARIANT                        =
   IT_EVENTS                         =
   IT_EVENT_EXIT                     =
   IS_PRINT_LVC                      =
   IS_REPREP_ID_LVC                  =
   I_SCREEN_START_COLUMN             = 0
   I_SCREEN_START_LINE               = 0
   I_SCREEN_END_COLUMN               = 0
   I_SCREEN_END_LINE                 = 0
   I_HTML_HEIGHT_TOP                 =
   I_HTML_HEIGHT_END                 =
   IT_ALV_GRAPHICS                   =
   IT_EXCEPT_QINFO_LVC               =
   IR_SALV_FULLSCREEN_ADAPTER        =
 IMPORTING
   E_EXIT_CAUSED_BY_CALLER           =
   ES_EXIT_CAUSED_BY_USER            =
  TABLES
    T_OUTTAB                          =
 EXCEPTIONS
   PROGRAM_ERROR                     = 1
   OTHERS                            = 2
          .
```
参数基与REUSE_ALV_GRID_DISPLAY本一致，不过由于这是REUSE_ALV_GRID_DISPLAY的改进版，有一些差别，其功能在一些方面也更强
1. 类型名称优化(不用声明 SLIS 类型池)
  SLIS_LAYOUT_ALV => LVC_S_LAYO
  SLIS_T_FIELDCAT => LVC_T_FCAT
  SLIS_FIELDCAT   => LVC_S_FCAT
  ...
2.设置**单元格编辑**属性
  内表ITAB中添加字段标识 field_styl TYPE lvc_t_styl.
  ```ABAP
  DATA: LT_STYL TYPE LVC_T_STYL,
        LS_STYL TYPE LVC_S_STYL.

  LOOP AT ITAB.
    IF ITAB-F1 >= 0 AND ITAB-F1 < 10.
      LS_STYL = cl_gui_alv_grid=>mc_style_disabled. "不可编辑
      MODIFY LT_STYL FROM LS_STYL.
    ELSE. ITAB-field_styl
      LS_STYL = cl_gui_alv_grid=>mc_style_enabled.  "可编辑
      MODIFY LT_STYL FROM LS_STYL.
    ENDIF.
    MODIFY ITAB-field_styl FROM LT_STYL.
    MODIFY ITAB.
  ENDLOOP.
  ```
3.对于F4帮助
若某字段在参照某表的字段，且已存在搜索帮助，则可在设置列属性时设置其搜索帮助
GS_FCAT-RET_TABLE = ''. "参考表
GS_FCAT-RET_FIELD = ''. "参考字段
APPEND GS_FCAT TO GT_FCAT.
若自需要定义F4帮助，需先设置
GS_FCAT-F4AVAILABL = 'X'.
并设置EVENT[].



