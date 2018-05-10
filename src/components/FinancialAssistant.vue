<template>
  <div class="bank">
    <Tag color="red" class="tag">算算我的贷款利息</Tag>
    <br>
    <Row type="flex" justify="start">
      <Col span="4">贷款额度</Col>
      <Col span="6">
        <Input v-model="totalCredit" clearable ></Input>
      </Col>
      <Col span="4" class="space-between">贷款时长</Col>
      <Col span="6">
        <Select v-model="creditYearModel" filterable clearable class="select">
          <Option v-for="item in creditYear" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
        </Select>
      </Col>
    </Row>
    <br>
    <Row>
      <Col span="4">贷款利率</Col>
      <Col span="6">
      <Select v-model="creditRateModel" filterable clearable class="select">
        <Option v-for="item in creditRate" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
      </Select>
      </Col>
      <Col span="4" class="space-between">还款方式</Col>
      <Col span="6">
      <Select v-model="payTypeModel" filterable clearable class="select">
        <Option v-for="item in payType" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
      </Select>
      </Col>
    </Row>
    <br>
    <Row>
      <Col span="4">利息总额</Col>
      <Col span="6">
      <Input v-model="totalInterest" disabled ></Input>
      </Col>
      <Col span="4" class="space-between">每月还款</Col>
      <Col span="6">
      <Input v-model="monthlyPay" disabled ></Input>
      </Col>
    </Row>
    <br>
    <br>
    <Tag color="blue" class="tag">算算我的投资收益</Tag>
    <br>
    <Row>
      <Col span="4">投资本金</Col>
      <Col span="6">
      <Input v-model="principal" clearable ></Input>
      </Col>
      <Col span="4" class="space-between">年化收益率</Col>
      <Col span="6">
      <Select v-model="debitRateModel" filterable clearable class="select">
        <Option v-for="item in debitRate" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
      </Select>
      </Col>
    </Row>
    <br>
    <Row>
      <Col span="4">续投方式</Col>
      <Col span="6">
      <Select v-model="debitTypeModel" filterable clearable class="select">
        <Option v-for="item in debitType" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
      </Select>
      </Col>
      <Col span="4" class="space-between">投资周期</Col>
      <Col span="6">
      <Select v-model="debitPeriodModel" filterable clearable class="select">
        <Option v-for="item in debitPeriod" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
      </Select>
      </Col>
    </Row>
    <br>
    <Row>
      <Col span="4">投资期数</Col>
      <Col span="6">
      <Select v-model="debitPeriodCountModel" filterable clearable class="select">
        <Option v-for="item in debitPeriodCount" :value="item.value" :key="item.value" class="option">{{ item.label }}</Option>
      </Select>
      </Col>
      <Col span="4" class="space-between">收益总额</Col>
      <Col span="6">
      <Input v-model="totalDebit" disabled ></Input>
      </Col>
    </Row>

    <Button class="button" type="success" long @click="callback" >返回星球广场</Button>


    <!--<p>新创意-承诺书-在这以太空间里，你就是我的唯一</p>-->
  </div>
</template>

<script>
export default {
  name: 'FinancialAssistant',
  data () {
    return {
      totalCredit: null,//贷款总额
      creditYearModel: 12,//贷款期数
      creditYear: [
        {
          value: 12,
          label: '1年12期'
        },
        {
          value: 24,
          label: '2年24期'
        },
        {
          value: 36,
          label: '3年36期'
        },
        {
          value: 60,
          label: '5年60期'
        },
        {
          value: 120,
          label: '10年120期'
        },
        {
          value: 180,
          label: '15年180期'
        },
        {
          value: 240,
          label: '20年240期'
        },
        {
          value: 300,
          label: '25年300期'
        },
        {
          value: 360,
          label: '30年360期'
        },
      ],
      payTypeModel:'本息',// 还款方式
      payType:[
        {
          value: '本息',
          label: '等额本息',
        },
        {
          value: '本金',
          label: '等额本金',
        }
      ],
      creditRate: [ // 贷款利率
        {
          value: 2.75,
          label: '公积金贷款 - 2.75'
        },
        {
          value: 4.35,
          label: '商业贷款基准利率 - 4.35'
        },
        {
          value: 4.785,
          label: '商业贷款1.1倍利率 - 4.785'
        },
        {
          value: 3.698,
          label: '商业贷款85折利率 - 3.698'
        },
        {
          value: 3.045,
          label: '商业贷款7折利率 - 3.045'
        },
      ],
      creditRateModel: 2.75,
      creditRateTable: [ // 贷款利率
      [{
        value: 2.75,
        label: '公积金贷款 - 2.75'
      },
        {
          value: 4.35,
          label: '商业贷款基准利率 - 4.35'
        },
        {
          value: 4.785,
          label: '商业贷款1.1倍利率 - 4.785'
        },
        {
          value: 3.698,
          label: '商业贷款85折利率 - 3.698'
        },
        {
          value: 3.045,
          label: '商业贷款7折利率 - 3.045'
        },],
      [{
        value: 2.75,
        label: '公积金贷款 - 2.75'
      },
        {
          value: 4.75,
          label: '商业贷款基准利率 - 4.75'
        },
        {
          value: 5.225,
          label: '商业贷款1.1倍利率 - 5.225'
        },
        {
          value: 4.038,
          label: '商业贷款85折利率 - 4.038'
        },
        {
          value: 3.325,
          label: '商业贷款7折利率 - 3.325'
        },],
      [{
        value: 3.25,
        label: '公积金贷款 - 3.25'
      },
        {
          value: 4.9,
          label: '商业贷款基准利率 - 4.9'
        },
        {
          value: 5.39,
          label: '商业贷款1.1倍利率 - 5.39'
        },
        {
          value: 4.165,
          label: '商业贷款85折利率 - 4.165'
        },
        {
          value: 3.43,
          label: '商业贷款7折利率 - 3.43'
        },],
    ],
//      totalInterest: null, // 贷款
//      monthlyPay:null, // 月还款
      principal: null, // 本金
      debitRate: [ // 年化存款利率
        {
          value: 3.9,
          label: '余额宝 - 3.9'
        },
        {
          value: 4.5,
          label: '平安银行工资理财 - 4.5'
        },
        {
          value: 5.05,
          label: '平安银行和盈系列 - 5.05'
        },
        {
          value: 4.3,
          label: '招商银行朝朝盈 - 4.3'
        },
        {
          value: 5.2,
          label: '平安财富宝富盈360 - 5.2'
        },
      ],
      debitRateModel: 3.9,
      debitTypeModel:'存本', // 续存类型
      debitType:[
        {
          value: '存本',
          label: '存本取息'
        },
        {
          value: '本息',
          label: '本息滚投'
        },
      ],
      debitPeriodModel: 360, // 投资周期
      debitPeriod:[
        {
          value: 30,
          label: '30天'
        },
        {
          value: 60,
          label: '60天'
        },
        {
          value: 90,
          label: '90天'
        },
        {
          value: 180,
          label: '180天'
        },
        {
          value: 270,
          label: '270天'
        },
        {
          value: 360,
          label: '360天'
        },
      ],
      debitPeriodCountModel: 1, // 投资期数
      debitPeriodCount:[
        {
          value: 1,
          label: '1期',
        },
        {
          value: 2,
          label: '2期',
        },
        {
          value: 3,
          label: '3期',
        },
        {
          value: 4,
          label: '4期',
        },
        {
          value: 5,
          label: '5期',
        },
        {
          value: 6,
          label: '6期',
        },
        {
          value: 7,
          label: '7期',
        },
        {
          value: 8,
          label: '8期',
        },
        {
          value: 9,
          label: '9期',
        },
        {
          value: 10,
          label: '10期',
        },
        {
          value: 11,
          label: '11期',
        },
        {
          value: 12,
          label: '12期',
        },
      ],
//      totalDebit: null,
    }
  },
  watch: {
    creditYearModel: function (val, oldVal) {
      if(val != oldVal){
        console.log('value change: ',val)
        if (val <= 12) {
          this.creditRate = this.creditRateTable[0];
          this.creditRateModel = this.creditRate[0].value;
        }
        else if(val >=13 && val <= 60) {
          this.creditRate = this.creditRateTable[1];
          this.creditRateModel = this.creditRate[0].value;
        }
        else {
          this.creditRate = this.creditRateTable[2];
          this.creditRateModel = this.creditRate[0].value;
        }
      }
    }
  },
  computed: {
    totalDebit: function () {
//      var dayRate = this.debitRateModel*this.debitPeriodModel/365/100;
//      return this.principal * dayRate * this.debitPeriodCountModel;
      return this.calcTotalDebit();
    },
    monthlyPay: function () {
      return this.calcMonthlyPay();
    },
    totalInterest: function () {
      return this.monthlyPay * this.creditYearModel - this.totalCredit;
    },
  },
  methods: {
    calcMonthlyPay: function () {
      var monthlyRate = this.debitRateModel/12/100;
      if(this.payTypeModel == '本息') {
        return this.totalCredit * monthlyRate * Math.pow(1 + monthlyRate, this.creditYearModel) / ( Math.pow(1 + monthlyRate, this.creditYearModel) -1 );
      } else if(this.payTypeModel == '本金') {
        var monthlyCredit = this.totalCredit/this.creditYearModel;
        return (this.totalCredit - monthlyCredit * 1) * monthlyRate + monthlyCredit;
      }
    },
    calcTotalCredit: function () {

    },
    calcTotalDebit: function () {
      var dayRate = this.debitRateModel*this.debitPeriodModel/365/100;
      if(this.debitTypeModel == '存本') {
        return this.principal * dayRate * this.debitPeriodCountModel;
      } else if(this.debitTypeModel == '本息') {
        return this.principal * (Math.pow(dayRate,(this.debitPeriodCountModel+1))-dayRate)/(dayRate-1);
      }
    },
    callback: function () {
      this.$emit('back');
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .bank{
    padding: .5rem;
    width: 100%;
    height: 100%;
    font-size: .25rem;
    z-index: 998;
  }
  .select,.ivu-input-type{
    width: 2rem;
    margin-left: .1rem;
  }
  .option{
    padding-left: .1rem;
  }
  .ivu-col-span-4{
    text-align: center;
    line-height: .64rem;
  }
  .ivu-col-span-6{
    text-align: left;
  }
  .ivu-input-disabled{
    color: black;
  }
  .space-between{
    margin-left: .5rem;
  }
  .tag{
    height: .6rem;
    font-size: .32rem;
    line-height: .6rem;
    margin: 0 0 .4rem .2rem;
  }
</style>
