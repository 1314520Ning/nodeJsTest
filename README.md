# 1 git使用
## 001 git上传
- 下载库
- git clone 库地址
```sh
git clone https://gitee.com/ningruixiang/test.git
```
- 先下载云端最新版
- git pull origin + 分支名
```sh
git pull origin 
```
- 查询状态
```sh
git status
```
- 上传缓存区
- file = 文件
```sh
git add <file>
```
- 上传本地
  git commit -m "上传者 >> 上传名称"

- 上传云端
- git push origin + 分支名
```sh
git push origin
```

## 002 git分支合并
- 创建分支(创建一个名为div的分支)
```sh
git checkout -b dev
```
- 查询状态
```sh
git status
```
- 上传缓存区
- file = 文件
```sh
git add <file>
```
- 上传本地
```sh
git commit -m "dev-020-新增注释"
```
-切换分支
```sh
git checkout master
```
- 先下载云端最新版
- git pull origin + 分支名
```sh
git pull origin 
```
- 合并
```sh
git merge dev
```
- 查询状态
```sh
git status
```
- 上传云端
- git push origin + 分支名
```sh
git push origin
```
- NRX20040823
# 2 大赛
## 001 启动GitStack

- 安装后会自动启动
- 第二次：windows服务 >> 控制面板>>windows工具 >> 服务 >> GitStack>> 右键启动
- 80端口，不能被占用，apache，nginx默认站点会占用80

## 002 建立远程仓库

- 登录地址：http://localhost/registration/login
- 默认用户名密码 admin/admin
- 建仓库 ，不能写中文
- 建立用户，不能写中文
- 将用户和仓库绑定


# 3 笔记
## 前端+后端（前端为主要）
### 001 主子表写法-子表只显示不做修改或选择弹窗 >>缺陷:下拉选框无法及时更新数据，选择弹窗不清楚
- import{get方法}from"@/文件位置"
- 写到要操作的按纽内
- for循环
- 跟随子表内id变动而变动设为列表数据
- 以产品建模为例
```template
//主要
<el-table-column label="设备" prop="equipmentId" width="180">
            <template #default="scope">
              <el-select
                v-model="scope.row.equipmentId"
                placeholder="请选择设备"
                disabled
              >
                <el-option
                  v-for="item in equipmentIdQuery"
                  :key="item.dictValue"
                  :label="item.dictLabel"
                  :value="Number(item.dictValue)"
                />
              </el-select>
            </template>
          </el-table-column>
          //跟随变动的列表
          <el-table-column label="型号" prop="equipmentModel" width="180">
          </el-table-column>
          <el-table-column label="编码" prop="equipmentCode" width="180">
          </el-table-column>
          <el-table-column label="生产日期" prop="equipmentProductDate" width="180">
          </el-table-column>
          <el-table-column label="生产批号" prop="equipmentBatchNo" width="180">
          </el-table-column>
          <el-table-column label="供应商" prop="equipmentManufacturer" width="180">
          </el-table-column> 
```
```javaScipt
import {
  getEquipmentInfo,
} from "@/api/baseInfo/equipmentInfo";

/** 修改按钮操作 */
function handleUpdate(row) {
  reset();
  const _id = row.id || ids.value;
  getFactoryModel(_id).then((response) => {
    form.value = response.data;
    mesFactoryModelDetailList.value = response.data.mesFactoryModelDetailList;
    //开始操作位置
    for (let index = 0; index < mesFactoryModelDetailList.value.length; index++) {
      getEquipmentInfo(mesFactoryModelDetailList.value[index].equipmentId).then(responst=>{
        console.log(responst.data);
        // let定义一个数组简化写法
        // let data = responst.data;
        mesFactoryModelDetailList.value[index].equipmentName = responst.data.name;
        mesFactoryModelDetailList.value[index].equipmentModel = responst.data.model;
        mesFactoryModelDetailList.value[index].equipmentManufacturer = responst.data.manufacturer;
        mesFactoryModelDetailList.value[index].equipmentBatchNo = responst.data.batchNo;
        mesFactoryModelDetailList.value[index].equipmentProductDate = responst.data.productDate;
        mesFactoryModelDetailList.value[index].equipmentCode = responst.data.code;
      })
    }
    //结束
    console.log(mesFactoryModelDetailList.value);
    open.value = true;
    csgo.value = true;
    title.value = "修改工厂建模";
  });
}
```

### 002 倒计时
- 考试所用

- 用时间戳转换当前时间和最终时间
- 最终时间减去当前时间得到倒计时时间
- 将时间戳转换为时分秒
- 后台：
```java
// /** 结束时间 */
//    @ApiModelProperty(value="结束时间",example = "2021-09-10-12:24:36")
//    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
//    @Excel(name = "结束时间", width = 30, dateFormat = "yyyy-MM-dd hh:mm:ss")
//    private Date endDate;

```
```html
 <p v-if="isCountdownReady" style="color:red">
 剩余时间：{{ hours }}小时 {{ minutes }}分钟 {{ seconds }}秒
</p>
```
- 前台
```renderscript
import {listEnddate,} from "@/api/system/enddate";
import { ref, onMounted, onUnmounted, watch } from "vue";

const endDate = ref(null);
const isCountdownReady = ref(false);

function getList() {
  listEnddate({}).then(response =>{
 console.log(response);
    endDate.value = new Date(response.rows[0].endDate); // 假设返回的是日期字符串，需要转换为Date对象
    console.log(endDate.value);
    isCountdownReady.value = true; // 数据加载完毕，准备开始倒计时
  })
}

getList();

const now = ref(Date.now());
let endTime; // 初始化为undefined，将在数据加载后计算
let intervalId = null;

const days = ref(0);
const hours = ref(0);
const minutes = ref(0);
const seconds = ref(0);
const diff = ref(0);

const countdown = () => {
  now.value = Date.now();
  diff.value = endTime - now.value;

  if (diff.value <= 0) {
    clearInterval(intervalId);
    console.log("时间已到！");
    return "时间已到！";
  }

  hours.value = Math.floor(
    (diff.value % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)
  );
  minutes.value = Math.floor((diff.value % (1000 * 60 * 60)) / (1000 * 60));
  seconds.value = Math.floor((diff.value % (1000 * 60)) / 1000);
};

watch(isCountdownReady, (newValue) => {
  if (newValue && !intervalId) {
    endTime = endDate.value.getTime();
    intervalId = setInterval(countdown, 1000);
    countdown(); // 初始调用一次
  }
});

onUnmounted(() => {
  if (intervalId) {
    clearInterval(intervalId);
  }
});
```

### 003 附件下载
- 下载组件编辑
- 后台
- 使用本地下载方法并将该方法放入下载白名单
>> 路径: com/neu/framework/config/SecurityConfig.java
```java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

 //省略其他代码
 private static final String[] PERMIT_LIST = {
         //将下载接口添加到可以访问的名单中
         "/common/download/resource"
 };
}
```
- 前端组件编辑
>> 名字：neu.js ||路径： @/utils/neu.js
```js
export function downResourceload(resource) {
   window.location.href = baseURL +
    "/common/download/resource?resource=" + encodeURI(resource) + "&delete=" + false;
}
```

- 思路 通过获取到主表id获取到附件表数据>>一个文件内不止一个附件 >> 用for循环取附件值
```html
     <div v-for="item in ids" :key="item">
      <el-button type="text" @click="fj(item)"> 附件：{{item.fileName}} </el-button>
    </div>
```
```js
//通过id取值
const fujianList = ref({
  complaintId:route.query.id
})
function getList(){
 listComplaintFiles(fujianList.value).then(res=>{
    ids.value = res.rows
  })
}
//下载点击事件
function fj(item){
  console.log(item);
  downResourceload(item.filePath)
}
```

### 004 json字符串与数组的转化
-- 知识
```js
//将对象转化成字符串
let arr = [1,2,3];
JSON.stringify(arr);//'[1,2,3]'
typeof JSON.stringify(arr);//string

将对象转化为数组（对象）
let string = '[1,2,3]';
console.log(JSON.parse(string))//[1,2,3]
console.log(typeof JSON.parse(string))//object
```
- 将数组清空js
```
方法一：
var arr = [1,2,3];
console.log(arr);
arr.length = 0;使用length
console.log(arr);
方法二：
var arr = [1,2,3];
console.log(arr);
arr.splice(0); 使用splice()
console.log(arr)；
方法三：
使用[]
var arr = [1 ,2 ,3];
console.log(arr);
arr = []; 
console.log(arr);
```



### 005 js文件内数据向scss传值
- scss全局样式设置（例：背景图片设置）
- 代码详情
```scss
//先做一个对象用于取值
$background-image: url();

//在scss的样式内取值
body{
...
background:$background-image;
...
}
```
// 在js文件中写入
```js
document.body.style.backgroundImage = `url("${fullUrl}")`
//fullUrl要设置背景图片的路径
```

### 006 拖拽色块
- 数据库加入background做背景色储存容器
- 通过 table的属性row-style取行样式数据
- 加入监听事件监听鼠标拖动状态
- 加入判定，判断鼠标状态，传入背景色
```html
<template>
<!-- 色块本块 -->
<div ref="color" class="color">
      <p
        class="colorone"
        draggable="true"
        style="background-color: red"
      ></p>
    </div>
        <!-- table表格 -->
        <div ref="table">
      <el-table
        v-loading="loading"
        :data="mesBomList"
        :row-style="colorChange"    <!-- 行的 style 的回调方法，也可以使用一个固定的 Object 为所有行设置一样的 Style。 -->
        @cell-mouse-enter="mousein" <!-- 当单元格 hover 进入时会触发该事件 -->
        @selection-change="handleSelectionChange"
      >
      ...省略代码
      </el-table>
    </div>
</template>
```
```vue
<script setup name="MesBom">
import { onMounted, ref } from "vue";
// 拖拽色块
const color = ref(null); // 定义一个可响应的颜色元素引用
const table = ref(null); // 定义一个可响应的表格元素引用
const colortext = ref(""); // 定义一个存储颜色值的可响应变量
const divcolor = ref(null); // 定义一个存储当前鼠标悬停的div元素的可响应变量
const colordata = ref([]); // 定义一个存储颜色数据的可响应数组
const flag1 = ref(false); // 定义一个标记，表示是否开始拖动色块
const flag2 = ref(false); // 定义一个标记，表示是否释放了色块

function colorChange(row, rowIndex) { 
  let temp = row.row.background; // 获取行的背景颜色
  console.log(row); // 在控制台打印行的信息

  return { backgroundColor: temp }; // 返回行的样式对象，设置背景颜色
}// 行样式

// 当单元格 hover 进入时会触发该事件
function mousein(row, column, cell, event) { 
  if (flag1.value) { // 如果已经开始移动色块
    if (flag2.value) { // 并且如果已经释放了色块
      let id = row.id; // 获取行的ID
      getMesBom(id).then((res) => { // 从服务器获取该行的数据
        let datas = res.data; // 保存从服务器返回的数据
        datas.background = colortext.value; // 更新数据中的背景颜色值
        flag2.value = false; // 设置释放标记为false
        flag1.value = false; // 设置移动标记为false
        updateMesBom(datas).then((res) => { // 将更新后的数据发送回服务器
          getList(); // 刷新列表
        }); // 修改数据库
      });
    }
  }
}

onMounted(() => { // Vue的生命周期钩子，在组件挂载完成后执行
  color.value.addEventListener("dragstart", (e) => { // 监听颜色元素的拖动开始事件
    flag1.value = true; // 设置移动标记为true
    colortext.value = e.target.style.backgroundColor; // 保存当前目标元素的背景颜色
  });
  table.value.addEventListener("dragover", (e) => { // 监听表格元素的拖动经过事件
    divcolor.value = e.target; // 保存当前目标元素

    return; // 阻止默认行为，允许拖放
  });
  color.value.addEventListener("dragend", (e) => { // 监听颜色元素的拖动结束事件
    flag2.value = true; // 设置释放标记为true
  });
});
</script>
```

```css
<style scoped>
.colorone {
  width: 20px;
  height: 20px;
  background-color: rgba(255, 255, 0, 0.397);
}
.color {
  width: 20px;
  position: absolute;
  right: 0;
  z-index: 123;
}
</style>

```

### 文字转语音
```vue
<script setup name="MesBom">

const utternace = new SpeechSynthesisUtterance("hellow world!我的名字是智慧学习系统")

utternace.lang = "zh-CN";
utternace.pitch = 1;
utternace.rate = 1;
utternace.volume = 1;
function ring() {
  window.speechSynthesis.speak(utternace)
}
</script>
```



## 4 纯粹后台
### 001 Java函数定义方法
```
权限《访问修饰符》 返回值类型 方法名（参数列表 || 参数类型 参数名）{
方法体
 return 返回值; 
 }
```
```java
//     public TableDataInfo list(BidBidding bidBidding) //public 公共权限 TableDataInfo 返回值类型 list 函数名 BidBidding bidBidding 参数列表 BidBidding 参数类型 bidBidding 参数名
//        {
//        startPage();
//        List<BidBidding> list = bidBiddingService.selectBidBiddingList(bidBidding);//消息体
//        return getDataTable(list);//getDataTable(list) 返回值
//        }
```

###  002 列表内时间到达判定
- 需求:到达结束时间，列表状态为到期
- 问题：修改内不更新;
```java
//for (BidBidding bidding : list) {
//            BidBidding li = bidBiddingService.selectBidBiddingById(bidding.getId());
//            li.setStatus(0);
//        }
```

### 003 主子表（修改与新增）问题
- >>新增：新增后显示查看不了
- 解决方法：controller内关于物料档案的表应该做if判定为不能为空；
- >>修改：失败报错为
``` 
Error: 
### Error updating database.  Cause: java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'usage, remark) values
          
            (  3, 2, 1, null)
         , 
     ' at line 1
    The error may exist in file [C:\Users\null\Desktop\project\2023国赛题目\后台\后端框架\neu-carbon-mapper\target\classes\mapper\mesPlan\MesProductBomMapper.xml]
    The error may involve defaultParameterMap
    The error occurred while setting parameters
    SQL: insert into mes_product_bom_detail(bom_id, material_id, usage, remark) values                        (  ?, ?, ?, ?)          ,              (  ?, ?, ?, ?)          ,              (  ?, ?, ?, ?)          ,              (  ?, ?, ?, ?)
### Cause: java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'usage, remark) values
          
            (  3, 2, 1, null)
         , 
     ' at line 1
; bad SQL grammar []; nested exception is java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'usage, remark) values
          
            (  3, 2, 1, null)
         , 
     ' at line 1
    at request.js:99:29 
```
改正方法：

```
insert into mes_product_bom_detail(bom_id, material_id,  usage , remark) values 内的usage应用“ `` ”包裹
```
```
 insert into mes_product_bom_detail(bom_id, material_id, `usage`, remark) values 则为正确
```
但如果改成
```
insert into mes_product_bom_detail(bom_id, material_id, `usage`, remark) values
        <foreach item="item" index="index" collection="list" separator=",">
            (  #{item.bomId}, #{item.materialId}, #{item.`usage`}, #{item.remark})
        </foreach>
```
则会报：
```
nested exception is org.apache.ibatis.reflection.ReflectionException: There is no getter for property named '`usage`' in 'class com.neu.carbon.mes.domain.MesProductBomDetail
```
最终的正确代码更正为：
``` 
 insert into mes_product_bom_detail(bom_id, material_id, `usage`, remark) values
        <foreach item="item" index="index" collection="list" separator=",">
            (  #{item.bomId}, #{item.materialId}, #{item.usage}, #{item.remark})
        </foreach>
```
# 移动应用开发
## 
<<<<<<< HEAD
=======

# info 
- 2024
- 0.246851123410
- 0031567896415637
>>>>>>> dev
```json
{
    "refresh_token": "25.aba39a5d4ed4c5a60f32686c223f9045.315360000.2077666010.282335-120556927",
    "expires_in": 2592000,
    "session_key": "9mzdC3xnX6ptMFd+m9qcy9LT31j0AO3lB7msrclVB86mXLy9zrpE8FyJ5bzcZTFzPzdzmOZXooS+ijNdSrHZ6yDXupa03QM=",
    "access_token": "24.0d7ce584fd2a74b2fa80327fe54af97e.2592000.1764898010.282335-120556927",
    "scope": "public vis-ocr_ocr brain_ocr_scope brain_ocr_general brain_ocr_general_basic vis-ocr_business_license brain_ocr_webimage brain_all_scope solution_face brain_ocr_idcard brain_ocr_driving_license brain_ocr_vehicle_license vis-ocr_plate_number brain_ocr_plate_number brain_ocr_accurate brain_ocr_accurate_basic brain_ocr_receipt brain_ocr_business_license brain_qrcode brain_solution_iocr brain_ocr_handwriting brain_ocr_passport brain_ocr_vat_invoice brain_numbers brain_ocr_business_card brain_ocr_train_ticket brain_ocr_taxi_receipt vis-ocr_household_register vis-ocr_vis-classify_birth_certificate vis-ocr_港澳通行证 vis-ocr_台湾通行证 vis-ocr_机动车购车发票识别 vis-ocr_机动车检验合格证识别 vis-ocr_车辆vin码识别 vis-ocr_定额发票识别 vis-ocr_保单识别 vis-ocr_机打发票识别 vis-ocr_行程单识别 brain_ocr_vin brain_ocr_quota_invoice brain_ocr_household_register brain_ocr_birth_certificate brain_ocr_HK_Macau_pass brain_ocr_taiwan_pass brain_ocr_vehicle_invoice brain_ocr_vehicle_certificate brain_ocr_air_ticket brain_ocr_invoice brain_ocr_insurance_doc brain_formula brain_seal brain_ocr_facade brain_ocr_meter brain_doc_analysis brain_ocr_webimage_loc brain_bus_ticket brain_toll_invoice ai_custom_pro_apple ai_custom_testface1 ai_custom_kouzhaorenlianshibie ai_custom_chongwugou brain_ocr_medical_paper brain_ocr_doc_analysis_office brain_ferry_ticket brain_vat_invoice_verification brain_ocr_medical_detail brain_ocr_used_vehicle_invoice brain_vehicle_registration_certificate brain_ocr_online_taxi_itinerary brain_ocr_multi_idcard brain_ocr_mixed_multi_vehicle brain_ocr_weigth_note brain_ocr_ multiple_invoice brain_ocr_social_security_card brain_ocr_medical_report_detection brain_ocr_waybill brain_ocr_medical_summary brain_ocr_pen brain_ocr_brain_shopping_receipt brain_ocr_road_transport_certificate brain_form_table brain_ocr_remove_handwriting brain_ocr_ doc_crop_enhance brain_ocr_health_report brain_document_restructure brain_ocr_three_factors_verification brain_ocr_four_factors_verification brain_ocr_bank_receipt_new brain_ocr_marriage_certificate brain_ocr_hk_macau_taiwan_exitentrypermit brain_ocr_two_factors_verification brain_doc_classify brain_textmind_textreview brain_textmind_textdiff brain_ocr_real_estate_certificate brain_overseas_passport brain_ccount_opening brain_food_business_license brain_general_struct brain_food_product_license brain_xmind_parser_rest brain_xmind_extract brain_divorce_certificate_scope brain_xmind_parser_paddleocr_vl wise_adapt lebo_resource_base lightservice_public hetu_basic lightcms_map_poi kaidian_kaidian ApsMisTest_Test权限 vis-classify_flower lpq_开放 cop_helloScope ApsMis_fangdi_permission smartapp_snsapi_base smartapp_mapp_dev_manage iop_autocar oauth_tp_app smartapp_smart_game_openapi oauth_sessionkey smartapp_swanid_verify smartapp_opensource_openapi smartapp_opensource_recapi fake_face_detect_开放Scope vis-ocr_虚拟人物助理 idl-video_虚拟人物助理 smartapp_component smartapp_search_plugin avatar_video_test b2b_tp_openapi b2b_tp_openapi_online smartapp_gov_aladin_to_xcx",
    "session_secret": "7194414088fc39b6f71b134608f9a745"
}
```
