<template>
  <div>
    <div class="crumbs">
      <!-- 面包屑-->
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-lx-calendar"></i> 工单中心
        </el-breadcrumb-item>
        <el-breadcrumb-item>维修开单</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="container">
      <h2 id="keHuForm">客户信息</h2>
      <el-form
          :model="totalForm"
          :rules="totalRules"
          ref="totalForm"
          label-width="120px"
          class="demo-ruleForm"
          :inline="true"
      >
        <el-form-item label="手机号" prop="phone">
          <el-input v-model="totalForm.phone" placeholder="手机号"></el-input>
        </el-form-item>
        <el-form-item label="姓名" prop="name">
          <el-input v-model="totalForm.name" placeholder="姓名"></el-input>
        </el-form-item>
        <el-form-item label="生日">
          <el-form-item prop="birth">
            <el-date-picker
                type="date"
                placeholder="选择日期"
                v-model="totalForm.birth"
                style="width: 100%;"
            ></el-date-picker>
          </el-form-item>
        </el-form-item>
        <el-form-item label="客户备注" prop="beiZhu">
          <el-input v-model="totalForm.uRemark" placeholder="备注信息"></el-input>
        </el-form-item>

        <h2 id="carForm">车辆信息</h2>

        <el-form-item label="车牌号" required prop="cNumber">
          <el-input
              placeholder="车牌后五位"
              v-model="totalForm.cNumber"
              style="width:200px"
              class="input-with-select"
          >
            <template v-slot:prepend>
              <el-select v-model="carNumberSelect" placeholder="陕A" style="width:70px">
                <el-option label="陕A" value="陕A"></el-option>
                <el-option label="陕U" value="陕U"></el-option>
                <el-option label="陕U" value="0"></el-option>
              </el-select>
            </template>
          </el-input>
        </el-form-item>
        <el-form-item label="VIN码" prop="vin">
          <el-input placeholder="输入完毕点击放大镜查询" v-model="totalForm.vin" class="input-with-select">
            <template v-slot:append>
              <el-button icon="el-icon-search"></el-button>
            </template>
          </el-input>
        </el-form-item>
        <br/>
        <el-form-item label="目前里程" prop="nowMileage">
          <el-input v-model="totalForm.nowMileage" type="number"></el-input>
        </el-form-item>
        <el-form-item label="下次保养里程" prop="nextMileage">
          <el-input v-model="totalForm.nextMileage" type="number" ></el-input>
        </el-form-item>
        <el-form-item label="下次保养日期">
          <el-form-item>
            <el-date-picker
                type="date"
                placeholder="选择日期"
                v-model="totalForm.nextDate"
                style="width: 100%;"
            ></el-date-picker>
          </el-form-item>
        </el-form-item>
        <br/>
        <el-form-item label="车辆备注">
          <el-input v-model="totalForm.cRemark" placeholder="备注"></el-input>
        </el-form-item>
      </el-form>

      <h2 id="fuWuData">服务项目</h2>
      <div class="fuWuMenu">
        <!--        <el-button size="small" type="primary" @click="fuWuDialogShow = true">选择服务项目</el-button>-->
        <el-button size="small" type="primary" @click="selectServe">选择服务项目</el-button>
        <el-button size="small" type="primary" @click="fuWuDialogShowB = true">新增服务项目</el-button>
      </div>
      <el-table :data="fuWuData" border :summary-method="xiangMuHeJiRules" show-summary
                style="width: 100%;marginTop:15px;margin-bottom:25px">
        <el-table-column type="index" width="50"></el-table-column>
        <el-table-column prop="name" label="项目名称"></el-table-column>
        <el-table-column prop="money" width="100" label="金额/元"></el-table-column>
        <el-table-column prop="sale" width="100" label="优惠/元"></el-table-column>
        <el-table-column prop="receivable" width="100" label="应收/元"></el-table-column>

        <el-table-column prop="people" width="150" label="施工人员">
          <template v-slot="scope">
            <el-select v-model="scope.row[scope.column.property]" @change="changePeople" placeholder="请选择">
              <el-option
                  v-for="item in employees"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id">
              </el-option>
            </el-select>
          </template>
        </el-table-column>

        <el-table-column label="操作" width="100">
          <template v-slot="scope">
            <el-button size="mini" type="danger" @click="fuwuDtaDelete(scope.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 项目选择弹出窗口 -->
      <el-dialog title="项目选择" v-model="fuWuDialogShow" width="80%">
        <div class="search">
          <el-input class="searchMargin" placeholder="项目名称"></el-input>
          <el-button type="primary" size="mini">查询</el-button>
        </div>

        <el-table :data="fuWuDatas" border @selection-change="fuWuDialogSelection" style="width: 100%;marginTop:25px">
          <el-table-column type="selection" width="55"></el-table-column>
          <el-table-column prop="name" label="项目名称"></el-table-column>
          <el-table-column prop="money" width="100" label="金额/元"></el-table-column>
          <el-table-column prop="sale" width="100" label="优惠/元"></el-table-column>
          <el-table-column prop="receivable" width="100" label="应收/元"></el-table-column>
          <el-table-column label="操作" width="100">
            <template v-slot="scope">
              <el-button
                  size="mini"
                  type="primary"
                  @click="selectFuWu( scope.row,scope.$index)"
                  plain
              >选择
              </el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination page-size="7" style="text-align: center" @current-change="getServes" background
                       layout="prev, pager, next"
                       :total="serveTotal">
        </el-pagination>
        <template v-slot:footer>
          <span class="dialog-footer">
            <el-button @click="fuWuDialogShow = false">取 消</el-button>
            <el-button type="primary" @click="fuWuDialogOk">确 定</el-button>
          </span>
        </template>
      </el-dialog>

      <!-- 新增项目弹出窗口 -->
      <el-dialog title="新增项目" v-model="fuWuDialogShowB" width="50%">
        <el-form ref="form" :model="newFuWuData" label-width="80px">
          <el-form-item label="项目名称">
            <el-input v-model="newFuWuData.name"></el-input>
          </el-form-item>
          <el-form-item label="金额/元">
            <el-input v-model="newFuWuData.jinE" type="number"></el-input>
          </el-form-item>
          <el-form-item label="优惠/元">
            <el-input v-model="newFuWuData.youHui" type="number"></el-input>
          </el-form-item>
        </el-form>
        <template v-slot:footer>
          <span class="dialog-footer">
            <el-button @click="fuWuDialogShowB = false">取 消</el-button>
            <el-button type="primary" @click="newFuWu">确 定</el-button>
          </span>
        </template>
      </el-dialog>


      <h2>使用商品</h2>
      <div class="shangpinMenu">
        <el-button size="small" type="primary" @click="selectMountings">选择使用商品</el-button>
      </div>
      <el-table :data="shangPinData" border show-summary :summary-method="shangPinHeJiRules"
                style="width: 100%;marginTop:15px;margin-bottom:25px">
        <el-table-column type="index" width="50"></el-table-column>
        <el-table-column prop="name" label="商品名称"></el-table-column>
        <el-table-column prop="brand" label="品牌"></el-table-column>
        <el-table-column prop="danJia" width="100" label="销售单价/元"></el-table-column>
        <el-table-column prop="shuLiang" width="100" label="数量">
          <template v-slot="scope">
            <el-input type="number" min="1" :max="scope.row.count" v-model="scope.row.shuLiang"
                      @change="changeShangPinCount(scope.row,scope.$index)" placeholder="请选择">
            </el-input>
          </template>
        </el-table-column>
        <el-table-column prop="zongJia" width="100" label="总价"></el-table-column>
        <el-table-column prop="carType" label="适用车型"></el-table-column>
        <el-table-column label="操作" width="100">
          <template v-slot="scope">
            <el-button size="mini" type="danger" @click="shangPinDataDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 使用商品弹出窗口 -->
      <el-dialog title="使用商品" v-model="shangPinDialogShow" width="80%">
        <el-form ref="form" :model="form" inline="true">
          <el-form-item>
            <el-input class="searchMargin" width="55" placeholder="商品名称"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input class="searchMargin" width="55" placeholder="品牌"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input class="searchMargin" width="55" placeholder="规格型号"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input class="searchMargin" width="55" placeholder="出厂编码"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input class="searchMargin" width="55" placeholder="OE号"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input class="searchMargin" width="55" placeholder="适用车型"></el-input>
          </el-form-item>
          <el-form-item label="是否有库存">
            <el-switch
                v-model="switchIs"
                active-color="#13ce66"
                inactive-color="#ff4949">
            </el-switch>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" size="mini" style="margin-left: 20px">查询</el-button>
          </el-form-item>
        </el-form>

        <el-table :data="shangPinDatas" border @selection-change="shangPinDialogSelection"
                  style="width: 100%;marginTop:25px">
          <el-table-column type="selection" width="55"></el-table-column>
          <el-table-column prop="name" label="商品名称"></el-table-column>
          <el-table-column prop="brand" label="品牌"></el-table-column>
          <el-table-column prop="modelVersion" label="规格型号"></el-table-column>
          <el-table-column prop="factoryCode" label="出厂编码"></el-table-column>
          <el-table-column prop="oe" label="OE号"></el-table-column>
          <el-table-column prop="salePrice" width="100" label="销售价/元"></el-table-column>
          <el-table-column prop="count" width="100" label="库存"></el-table-column>
          <el-table-column prop="units" width="100" label="单位"></el-table-column>
          <el-table-column prop="carType" label="适用车型"></el-table-column>
          <el-table-column label="操作" width="100">
            <template v-slot="scope">
              <el-button
                  size="mini"
                  type="primary"
                  @click="selectShangPin(scope.row)"
                  plain
              >选择
              </el-button>
            </template>
          </el-table-column>
        </el-table>

        <template v-slot:footer>
          <span class="dialog-footer">
            <el-button @click="shangPinDialogShow = false">取 消</el-button>
            <el-button type="primary" @click="shangPinDialogOk">确 定</el-button>
          </span>
        </template>
      </el-dialog>


      <h2>附加费</h2>
      <div class="shangpinMenu">
        <el-button size="small" type="primary" @click="fuJia = true">新增附加费</el-button>
      </div>
      <el-table :data="fuJiaData" border :summary-method="fuJiaHeJiRules" show-summary
                style="width: 100%;marginTop:15px;margin-bottom:25px">
        <el-table-column type="index" width="50"></el-table-column>
        <el-table-column prop="name" label="附加费名称"></el-table-column>
        <el-table-column prop="jinE" label="金额"></el-table-column>
        <el-table-column prop="beiZhu" label="备注"></el-table-column>
        <el-table-column label="操作" width="100">
          <template v-slot="scope">
            <el-button size="mini" type="danger" @click="fuJiaDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 新增附加费弹出窗口 -->
      <el-dialog title="新增附加费" v-model="fuJia" width="80%">
        <el-form ref="form" :model="fuJiaFrom">
          <el-form-item label="附加费名称">
            <el-input v-model="fuJiaFrom.name" class="searchMargin" width="55" placeholder="附加费名称"></el-input>
          </el-form-item>
          <el-form-item label="金额/元">
            <el-input v-model="fuJiaFrom.jinE" type="number" class="searchMargin" width="55"
                      placeholder="金额/元"></el-input>
          </el-form-item>
          <el-form-item label="备注">
            <el-input v-model="fuJiaFrom.beiZhu" class="searchMargin" width="55" placeholder="备注"></el-input>
          </el-form-item>
        </el-form>
        <template v-slot:footer>
          <span class="dialog-footer">
            <el-button @click="fuJia = false">取 消</el-button>
            <el-button type="primary" @click="fuJiaDialogOk">确 定</el-button>
          </span>
        </template>
      </el-dialog>

      <!--      最底部确认条-->
      <el-card>
        <div class="botCard">
          <div>总计：<span style="color:red;font-weight: bold;font-size: 20px">￥{{ totalMoneys }}</span> 元
          </div>
          <div>
            <el-button type="primary" plain @click="tiTjiao">提交</el-button>
            <el-button type="warning" plain>保存</el-button>
          </div>
        </div>
      </el-card>

      <!--     确认的弹出框-->
      <el-dialog title="提交成功" v-model="tiJiaoDialog" width="50%">
        <div>订单提交成功，当前状态为：<span style="color: #20a0ff">[待施工]</span></div>
        <br/>
        <div>可在 <span style="color: #20a0ff;text-decoration: underline;cursor: pointer">订单中心-待施工</span> 中进行查看</div>
        <div>点击 确定 跳转至首页</div>
        <template v-slot:footer>
          <span class="dialog-footer">
            <el-button @click="wanGong">完工并结算</el-button>
            <el-button type="primary" @click="dingDanWanCheng">确 定</el-button>
          </span>
        </template>
      </el-dialog>

      <!--      结算弹出框-->
      <el-dialog lock-scroll='false' v-model="jieSuanDialog" width="50%">
        <div class="jieSuanTop">
          <div class="number">陕A-10000</div>
          <div class="name">马帅帅</div>
        </div>
        <div class="jieSuanMid">
          <el-card class="card1">
            <h3>结算信息</h3><br>
            <el-form :model="totalMoney" label-width="80px">
              <el-form-item size="mini" label="服务费用">
                <el-input disabled v-model="totalMoney.fuWu"></el-input>
              </el-form-item>
              <el-form-item size="mini" label="商品费用">
                <el-input disabled v-model="totalMoney.shangPin"></el-input>
              </el-form-item>
              <el-form-item size="mini" label="附加费用">
                <el-input disabled v-model="totalMoney.fuJia"></el-input>
              </el-form-item>
              <el-form-item size="mini" label="总   计">
                <el-input disabled v-model="totalMoneys"></el-input>
              </el-form-item>
            </el-form>
          </el-card>
          <el-card class="card2">
            <h3>收款信息</h3><br>
            <el-form :model="shouKuanInfo" label-width="80px">
              <el-form-item size="mini" label="结算时间">
                <el-date-picker
                    type="datetime"
                    v-model="shouKuanInfo.dataTime"
                    placeholder="选择日期时间">
                </el-date-picker>
              </el-form-item>
              <el-form-item size="mini" label="收款方式">
                <el-select v-model="shouKuanInfo.method" placeholder="选择收款方式">
                  <el-option label="微信" value="1"></el-option>
                  <el-option label="支付宝" value="2"></el-option>
                  <el-option label="现金" value="3"></el-option>
                  <el-option label="其他" value="4"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item size="mini" label="收款人员">
                <el-select v-model="shouKuanInfo.people" placeholder="选择收款人员">
                  <el-option label="小王" value="1"></el-option>
                  <el-option label="小李" value="2"></el-option>
                  <el-option label="小张" value="3"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item size="mini" label="结算备注">
                <el-input v-model="shouKuanInfo.beiZhu"></el-input>
              </el-form-item>
            </el-form>
          </el-card>
        </div>
        <template v-slot:footer>
          <span class="dialog-footer">
            <el-button>取 消</el-button>
            <el-button type="primary" @click="jieSuan">结 算</el-button>
          </span>
        </template>
      </el-dialog>


    </div>
  </div>
</template>

<script>
// import moment from 'moment'
// eslint-disable-next-line no-unused-vars
import {getEmployees, getServe} from '@/api/sheZhiZhongXin'
import {getMountings} from '@/api/kuCunZhongXin'

export default {
  name: "weiXiuKaiDan",
  data() {
    return {
      switchIs: true,
      shangpinS: false,
      shangpinSB: false,
      fuJia: false,
      optinonValue: '',
      options: [{
        value: '选项1',
        label: '黄金糕'
      }, {
        value: '选项2',
        label: '双皮奶'
      }, {
        value: '选项3',
        label: '蚵仔煎'
      }, {
        value: '选项4',
        label: '龙须面'
      }, {
        value: '选项5',
        label: '北京烤鸭'
      }],
      dialogVisible: false,
      dialogVisibleB: false,
      // --------------------------------------------------------------------------------------------------------------------------
      // 所有表格数据
      totalForm: {
        // 手机号
        phone: '',
        // 姓名
        name: '',
        // 生日
        birth: '',
        // 客户备注
        uRemark: '',
        // 车牌号
        cNumber: '',
        // vin
        vin: '',
        // 当前里程
        nowMileage: '',
        // 下次里程
        nextMileage: '',
        // 下次日期
        nextDate: '',
        // 车辆备注
        cRemark: '',
        // 服务项目
        serveList: '',
        // 使用配件
        mountingList: '',
        // 附加费
        extraList: '',
        // 创建时间
        createTime: '',
      },
      carNumberSelect: '',
      // 验证规则
      totalRules: {
        phone: [
          {required: true, message: "请输入手机号", trigger: "blur"},
          {min: 11, max: 11, message: "请输入11位手机号", trigger: "blur"}
        ],
        name: [
          {required: true, message: "请输入姓名", trigger: "blur"},
          {min: 1, max: 4, message: "请输入正确姓名", trigger: "blur"}
        ],
        cNumber: [
          {required: true, message: "请输入车牌号", trigger: "blur"},
          {min: 5, max: 5, message: "请输入5位车牌号", trigger: "blur"}
        ],
        vin: [
          {min: 17, max: 17, message: "请输入17位vin码", trigger: "blur"}
        ],
        nowMileage:[
          {min: 1, max: 6, message: "目前里程数异常", trigger: "blur"}
        ],
        nextMileage:[
          {min: 1, max: 6, message: "下次保养里程数异常", trigger: "blur"}
        ]

      },
      // 是否打开服务项目中的弹出框
      fuWuDialogShow: false,
      fuWuDialogShowB: false,
      // 服务项目弹出框的分页器
      serveTotal: '',
      // 企业已经设置好的服务项目
      fuWuDatas: [],
      // 服务项目表格数据
      fuWuData: [],
      // 员工信息
      employees: [],
      // 控制选择商品弹框的显示隐藏
      shangPinDialogShow: false,
      // 商品库存数据
      shangPinDatas: [],
      // 商品表格数据
      shangPinData: [],
      // --------------------------------------------------------------------------------------------------------------------------
      // 选择服务中多选框改变的值
      fuWuSelectionData: [],
      // 新增服务的值
      newFuWuData: {name: '', jinE: '', youHui: ''},


      // 选择商品中多选框改变的值
      shangPinSelectionData: [],


      // 附加费表格数据
      fuJiaData: [],
      // 附加费表单数据
      fuJiaFrom: {name: "", jinE: '', beiZhu: ''},

      // 底部最终总计金额
      totalMoney: {
        fuWu: 1,
        shangPin: 2,
        fuJia: 3
      },
      // 提交成功的弹出框
      tiJiaoDialog: false,
      // 结算弹出框
      jieSuanDialog: false,
      // 收款信息
      shouKuanInfo: {
        dataTime: '',
        method: '',
        people: '',
        beiZhu: ''
      }

    };
  },
  mounted() {
    this.getemployees()
  },
  methods: {
    // 获取员工信息
    getemployees() {
      getEmployees().then(res => {
        if (res.status !== 200) {
          this.$message.error(res.data)
          return
        }
        this.employees = res.data
        console.log(res.data)
        // this.peopleManagement = res.data
      })
    },
    // 打开选择服务项目时获取数据
    selectServe() {
      // 打开弹窗
      this.fuWuDialogShow = true
      // 获取数据
      this.getServes(1)
    },
    // 打开选择商品是获取数据
    selectMountings() {
      // 打开弹窗
      this.shangPinDialogShow = true
      // 获取数据
      this.getmounting(1)
    },


    // 获取商品数据
    getmounting(currentPage) {
      getMountings({currentPage}).then(res => {
        if (res.status !== 200) {
          this.$message.error(res.data)
          return
        }
        this.shangPinDatas = res.data.data
        console.log(res.data)
      })
    },

    // 获取服务项目
    getServes(currentPage) {
      getServe({currentPage}).then(res => {
        if (res.status !== 200) {
          this.$message.error(res.data)
          return
        }
        this.fuWuDatas = res.data.data
        this.serveTotal = res.data.total
        console.log(res.data)
      })
    },
    // 选择服务项目
    selectFuWu(e) {
      // e为选择的值
      // 将选择的值push加上people后push进服务项目表格
      let value = e
      value.people = ''
      this.fuWuData.push(value)
      // 关闭弹出框
      this.fuWuDialogShow = false
    },
    // 选择服务弹出框的多选
    fuWuDialogSelection(val) {
      // val为每次选择框状态改变后的所有被选中值
      this.fuWuSelectionData = val
    },
    // 选择服务弹出框的确定方法
    fuWuDialogOk() {
      // 判断是否没有选中任何项目
      if (this.fuWuSelectionData.length === 0) {
        this.$confirm('你好像并没有选择任何项目，是否需要继续选择?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          // 点击确定，什么都不做
        }).catch(() => {
          // 取消 关闭弹出框
          this.fuWuDialogShow = false
        });
      } else {
        // 多选框中选定了内容 将选中的值添加people后连接  关闭弹出框
        let value = this.fuWuSelectionData
        value.forEach((item, index) => {
          value[index].people = ''
        })
        this.fuWuData = this.fuWuData.concat(value)
        this.fuWuDialogShow = false
      }
    },
    // 新增服务确定
    newFuWu() {
      console.log(this.newFuWuData)
      // 如果名称和金额不为空
      let value = this.newFuWuData
      if (value.name != '' && value.jinE != '') {
        // 计算出总金额之后push进表格数据
        let total = ''
        if (value.youHui != '') {
          total = value.jinE - value.youHui
        } else {
          total = value.jinE
        }
        value.yingShou = total
        value.people = ''
        this.fuWuData.push(value)
        // 询问是否需要将该新增项目加入到设置好的项目中
        this.$confirm('是否需要将该项目加入到常用项目列表中', '添加成功', {
          confirmButtonText: '添加',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          // 需要添加
          this.fuWuDatas.push(value)
          this.$message({
            type: 'success',
            message: '添加成功'
          })
          this.fuWuDialogShowB = false
          this.newFuWuData = {name: '', jinE: '', youHui: ''}

        }).catch(() => {
          // 不要添加
          this.fuWuDialogShowB = false
          this.newFuWuData = {name: '', jinE: '', youHui: ''}
        })
      } else {
        this.$message({
          message: '新增项目的名称和金额不能为空',
          type: 'warning'
        });
      }
    },
    // 选择项目中合计规则
    xiangMuHeJiRules(param) {
      // columns为表头数据 包含序号 名称等
      // data为每行数据
      console.log(param)
      const {columns, data} = param;
      // 计算总价
      const sums = [];
      columns.forEach((column, index) => {
        // 第一行为总计
        if (index === 0) {
          sums[index] = '总计';
          return;
        }
        if (index === 1) {
          sums[index] = ''
          return
        }
        if (index === 5) {
          sums[index] = ''
          return
        }
        // map 数组方法 返回一个新数组 新数组为函数处理后的值
        // Number方法把对象的值转化为数字
        // every 数组方法 检测数组元素中的值是否都符合条件
        // reduce 数组方法 累加器 params1：初始值，也是最终返回值 params2：当前元素
        const values = data.map(item => Number(item[column.property]));
        // 判断当前是否为数字
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);
            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          // 给全部总金额赋值
          if (index == 4) {
            this.totalMoney.fuWu = sums[4]
          }
          sums[index] += ' 元';
        } else {
          sums[index] = '-';
        }
      });

      //  返回最终结果
      return sums;
    },
    // 删除服务项目
    fuwuDtaDelete(e) {
      this.fuWuData.splice(e, 1)
      this.$message({
        type: 'success',
        message: '删除成功'
      })
    },

    // 选择商品
    selectShangPin(e) {
      let value = e
      value.shuLiang = 1
      value.danJia = value.salePrice
      value.zongJia = value.danJia * value.shuLiang
      this.shangPinData.push(value)
      this.shangPinDialogShow = false
    },
    // 商品合计规则
    shangPinHeJiRules(param) {
      const {columns, data} = param;
      const sums = [];
      columns.forEach((column, index) => {
        // 第一行为总计
        if (index === 0) {
          sums[index] = '总计';
          return;
        }
        if (index === 5) {
          const values = data.map(item => Number(item[column.property]));
          // 判断当前是否为数字
          if (!values.every(value => isNaN(value))) {
            sums[index] = values.reduce((prev, curr) => {
              const value = Number(curr);
              if (!isNaN(value)) {
                return prev + curr;
              } else {
                return prev;
              }
            }, 0);
            // 给全部总金额赋值
            if (index == 5) {
              this.totalMoney.shangPin = sums[5]
            }
            sums[index] += ' 元';
          } else {
            sums[index] = '-';
          }
        }
      });
      //  返回最终结果
      return sums;
    },
    // 商品数量改变后改变该商品的总价
    changeShangPinCount(e1, e2) {
      // e1为当前行的数据 e2为当前行的索引
      let count = e1.shuLiang
      // 判断输入大于0
      if (count <= 0) {
        this.$message.error('数量必须大于等于1')
      }
      // 计算出总价后对表格数据重新赋值
      this.shangPinData[e2].zongJia = count * this.shangPinData[e2].danJia
    },
    // 删除使用商品
    shangPinDataDelete(e) {
      this.shangPinData.splice(e, 1)
      this.$message({
        type: 'success',
        message: '删除成功'
      })
    },
    //选择商品弹窗的多选
    shangPinDialogSelection(val) {
      // val为每次选择框状态改变后的所有被选中值
      this.shangPinSelectionData = val
    },
    // 选择商品弹出框的确定方法
    shangPinDialogOk() {
      // 判断是否没有选中任何项目
      if (this.shangPinSelectionData.length === 0) {
        this.$confirm('你好像并没有选择任何商品，是否需要继续选择?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          // 点击确定，什么都不做
        }).catch(() => {
          // 取消 关闭弹出框
          this.shangPinDialogShow = false
        });
      } else {
        // 多选框中选定了内容  给每项数量变为1 计算总价  关闭弹出框
        let value = this.shangPinSelectionData
        value.forEach((item, index) => {
          value[index].shuLiang = 1
          value[index].danJia = value[index].salePrice
          value[index].zongJia = value[index].shuLiang * value[index].danJia
        })
        this.shangPinData = this.shangPinData.concat(value)
        this.shangPinDialogShow = false
      }
    },

    //新增附加费弹框的确认方法
    fuJiaDialogOk() {
      // 判断金额名称不为空
      if (this.fuJiaFrom.name != '' && this.fuJiaFrom.jiNE != '') {
        this.fuJiaData.push(this.fuJiaFrom)
        this.fuJia = false
        this.fuJiaFrom = {name: '', jinE: '', beiZhu: ''}
      } else {
        this.$confirm('附加费的金额或名称为空，是否需要继续编写?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          // 点击确定，什么都不做
        }).catch(() => {
          // 取消 关闭弹出框
          this.fuJia = false
        });
      }

    },
    // 附加费表格合计规则
    fuJiaHeJiRules(param) {
      const {columns, data} = param;
      const sums = [];
      columns.forEach((column, index) => {
        // 第一行为总计
        if (index === 0) {
          sums[index] = '总计';
          return;
        }
        if (index === 2) {
          const values = data.map(item => Number(item[column.property]));
          // 判断当前是否为数字
          if (!values.every(value => isNaN(value))) {
            sums[index] = values.reduce((prev, curr) => {
              const value = Number(curr);
              if (!isNaN(value)) {
                return prev + curr;
              } else {
                return prev;
              }
            }, 0);
            // 给全部总金额赋值
            if (index == 2) {
              this.totalMoney.fuJia = sums[2]
            }
            sums[index] += ' 元';
          } else {
            sums[index] = '-';
          }
        }
      });
      //  返回最终结果
      return sums;
    },
    // 删除附加费
    fuJiaDelete(e) {
      this.fuJiaData.splice(e, 1)
      this.$message.success('删除成功')
    },


    // 提交
    tiTjiao() {
      this.$refs['totalForm'].validate((valid, error) => {
        if (!valid) {
          let errorMessage = ''
          for (let item in error) {
            error[item].forEach(item => {
              errorMessage += item.message + ' '
            })
          }
          this.$message.error(errorMessage)
        } else {
          if (this.fuWuData.length === 0) {
            this.$message.error('没有选择服务项目')
          } else {
            // console.log('this.totalForm')
            // console.log(this.totalForm)
            // 客户生日
            console.log(new Date(this.totalForm.birth).getTime())
            // 当前创建时间
            console.log(new Date().getTime())
            // 最终传递给后端的值
            let params = this.totalForm
            // 如果用户备注存在 转换为时间戳
            if (params.birth !== '') {
              params.birth = new Date(params.birth).getTime()
            }
            // 创建时间
            params.createTime = new Date().getTime()
            // 客户下次保养时间
            if (params.nextDate !== '') {
              params.nextDate = new Date(params.nextDate).getTime()
            }

            console.log('this.fuWuData')
            console.log(this.fuWuData)
            let serveList = ''
            // 1,10.2.10,3.10    .分割 1为项目 10为员工
            this.fuWuData.forEach((item)=>{
              serveList += item.id + ','
              if(item.people !==''){
                serveList += item.people + '.'
              }else{
                serveList += 'N' + '.'
              }
            })
            console.log(serveList)

            // console.log('this.shangPinData')
            // console.log(this.shangPinData)
            // console.log('this.fuJiaData')
            // console.log(this.fuJiaData)
          }
        }
        // if(valid){
        //   console.log(valid)
        // }
      })
    },
    // 完工并结算
    wanGong() {
      this.$confirm('完工结算后订单将无法修改，是否继续?', '完工并结算', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 关闭提交弹出框
        this.tiJiaoDialog = false
        // 打开结算弹框
        this.jieSuanDialog = true

      })
    },
    // 订单完成弹出框确定
    dingDanWanCheng() {
      this.$message.success('操作成功')
      this.tiJiaoDialog = false
      // 关闭当前标签页
      this.$store.commit("closeCurrentTag", {
        $router: this.$router,
        $route: this.$route
      });
      // 跳转路由
      this.$router.push('dashboard')
    },
    // 结算弹框中的结算按钮
    jieSuan() {
      this.$confirm('确认结算吗', '结算', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$router.push('/weiXiuDan')
        // 关闭提交弹出框
        this.jieSuanDialog = false
      })
    },


    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },

    handleRead(index) {
      const item = this.unread.splice(index, 1);
      console.log(item);
      this.read = item.concat(this.read);
    },
    handleDel(index) {
      const item = this.read.splice(index, 1);
      this.recycle = item.concat(this.recycle);
    },
    handleRestore(index) {
      const item = this.recycle.splice(index, 1);
      this.read = item.concat(this.read);
    },

    getSummaries(param) {
      const {columns, data} = param;
      const sums = [];
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = "总价";
          return;
        }
        const values = data.map(item => Number(item[column.property]));
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);
            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          sums[index] += " 元";
        } else {
          sums[index] = "N/A";
        }
      });

      return sums;
    }
  },
  computed: {
    // 合计价格
    totalMoneys() {
      return this.totalMoney.fuJia + this.totalMoney.fuWu + this.totalMoney.shangPin
    },

    unreadNum() {
      return this.unread.length;
    }
  }
};
</script>

<style>
.message-title {
  cursor: pointer;
}

.handle-row {
  margin-top: 30px;
}

h2 {
  margin-bottom: 20px;
}

.search {
  display: flex;
  width: 50%;
}

.searchMargin {
  margin-right: 20px;
}

.botCard {
  display: flex;
  justify-content: space-between;
}

.jieSuanTop {
  display: flex;
  align-items: center;
  margin: 0 auto;
  width: 170px;
  font-weight: bold;
  font-size: 20px;
  justify-content: space-between;
  color: #409EFF;
}

.jieSuanMid {
  display: flex;
  margin-top: 10px;
  justify-content: space-between;
}

.jieSuanMid .card1 {
  width: 39%;
}

.jieSuanMid .card2 {
  width: 59%;
}

</style>

