<template>
  <div class="product-details-page product-details-common">
    <!--非基因检测详情页22222222-->
    <div>
      <div class="product-details-con">
        <!--视频区-->
        <div class="banner-video-area" v-if="productBase.bannerVideo">
          <!--视频前图-->
          <div class="video-mask">
            <img :src="productBase.bannerImage"
                 alt="">
            <div
              class="play-btn"
              @click="videoPlay"
            ></div>
          </div>
          <!--视频controlsList='nofullscreen nodownload noremote footbar x5­video-player-fullscreen="false"' -->
          <video
            id="mainVideo"
            controls controlslist='nofullscreen'
            x5-video-player-fullscreen="false"
            x-webkit-airplay="true" preload="none"
            webkit-playsinline="true"
            playsinline="true"
            width="100%"
            height="190"
            :src="productBase.bannerVideo"
          ></video>
        </div>
        <div class="details-banner-area" v-else>
          <div class="img-box" v-if="productBase.bannerImage !== undefined && productBase.bannerImage !== ''">
            <img :src="productBase.bannerImage" alt="">
          </div>
          <div class="company-name-box" v-if="productCompanyName !== undefined && productCompanyName !== ''">
            <i class="logo"></i>
            <span class="company-name">{{productCompanyName}}</span>
          </div>

        </div>

        <div class="product-choose-infor">
          <!--产品信息区-->
          <div class="product-choose-box">
            <h3 class="pro-name">{{productBase.name}}</h3>
            <p class="pro-sub-describe" v-if="productBase.subTitle !== undefined && productBase.subTitle!==''">
              {{productBase.subTitle}}</p>
            <div class="pro-tags-area">
              <div class="tags-box" v-if="productBase.labels !==undefined && productBase.labels!==''">
                <span v-for="(item,index) in productBase.labels" :key="index">{{item}} <i class="line"
                                                                                          v-if="index < productBase.labels.length-1"></i></span>
              </div>

              <div class="money-calculation-btn" v-if="trialShow && !goFlag.bohaiLifeFlag" @click="showCalculation">
                <i class="calculation-icon"></i>
                <span>保费试算</span>
              </div>

            </div>
        <!-- 渤海年金接入start -->
        <div class="product-bohaiLife-box" v-if='goFlag.bohaiLifeFlag'>
            <ul class="try-calculation-ul">
                   <li class="try-calculation-list">
                      <div class="key">投保性别及年龄</div>
                      <picker-item :data ='this.premiumLimit.tryCalDate' :vModel="this.premiumLimit.tryCalModel" ref='sexAge'></picker-item>
                   </li>
                   <li class="try-calculation-list">
                      <div class="key">存入金额</div>
                      <picker-item :data ='this.premiumLimit.tryCalNumDate' :vModel="this.premiumLimit.tryCalNumModel"></picker-item>
                   </li>
            </ul>
            <div class="try-calculation-total">
              <span class="total-key">
                <em class="key">累计可领金额</em>
                <em class="standard">按照80岁计算</em>
              </span>
              <span class="total-val">
                <em class="figure">{{this.transformMoney(sumNianjinPrice)}}</em>
                <em class="unit">元</em>
              </span>
            </div>
            <ul class="try-calculation-getway">
                <li class="try-calculation-list">
                  <span class="list-title">{{this.receivAgeShow}}时一次性领取</span>
                  <span class="list-val">
                    <em class="figure">{{this.transformMoney(isOnePrice)}}</em>
                    <em class="unit">元</em>
                  </span>
                </li>
               <li class="try-calculation-list">
                  <span class="list-title">{{this.receivAgeShow}}起每{{this.receiveCycleShow}}可领</span>
                  <span class="list-val">
                    <em class="figure">{{this.transformMoney(yearOrMonthPrice)}}</em>
                    <em class="unit">元</em>
                  </span>
                </li>
            </ul>
            <div class="pro-select-infor-box">
              <ul class="set-insurance-ul">
                <li class="set-insurance-list clearfix">
                  <div class="key">
                    <p>交费期间</p>
                  </div>
                  <div class="val">
                    <div class="input-item-box">
                        <slider-itemC :sliderList='payPeriods'
                                    :defaultValues="payPeriodsSelectIdx"
                                    @checkedItemIdx='itemCheckedPayPeriods'></slider-itemC>              

                    </div>
                  </div>
                </li>
                <li class="set-insurance-list">
                  <div class="key">
                    <p>领取年龄<i class="tips-icon" @click="showreceivAgePanel"></i></p>
                  </div>
                  <div class="val">
                    <slider-itemC :sliderList='receivAge' :defaultValues="receivAgeSelectIdx" @checkedItemIdx='itemCheckedreceivAgePeriods'></slider-itemC>
                  </div>
                </li>
                <li class="set-insurance-list">
                  <div class="key">
                    <p>领取方式<i class="tips-icon" @click="showreceivWayPanel"></i></p>
                  </div>
                  <div class="val">
                    <slider-itemC :sliderList='receiveCycle'
                                    :defaultValues="receiveCycleSelectIdx"
                                    @checkedItemIdx='itemCheckedreceiveCyclePeriods'></slider-itemC>
                  </div>
                </li>
              </ul>
            </div>
        </div>
        <!-- 渤海年金接入end -->
            <div class="price-choose-area" v-if="productPlans.length > 1">
              <!--选择多方案，关联责任列表-->
              <div class="price-choose-box" :class="plansNum">
                <div class="price-choose-item" :class="activePlansItem == index?'price-checked':''"
                     v-for="(item,index) in productPlans" :key="index"
                     @click="plansActive(index,item.amount,item.price.value,item)">
                  <div class="up-price"><em>{{item.price.value}}</em><i>{{item.price.unitName}}</i>起</div>
                  <div class="insurance-limit">{{item.planName}}</div>
                </div>
              </div>
            </div>
            <!--多方案责任列表-->
            <div class="insurance-infor-lists" v-if="productPlans.length > 1 && productLiabilities[activePlansAmount]">
              <ul>
                <li class="clearfix" v-for="(item,index) in productLiabilities[activePlansAmount]" :key="index">
                  <div class="key">{{item.display}}</div>
                  <div class="val">{{item.value}}</div>
                </li>
              </ul>
            </div>
            <!--如果是单方案的，只展示责任列表，不需要关联-->
            <div class="insurance-infor-lists" v-else-if="productLiabilities[activePlansAmount]">
              <ul data-b="b">
                <li class="clearfix" v-for="(item,index) in productLiabilities[activePlansAmount]" :key="index">
                  <div class="key">{{item.display}}</div>
                  <div class="val">{{item.value}}</div>
                </li>
              </ul>
            </div>
            <a class="check-insurance-details" @click="goGuaranteeDetails">{{isServerOrder?'查看服务详情':'查看保障详情'}}</a>
          </div>

          <!-- 教育楼层 -->
          <div class="insurance-classroom-area" v-if="classroomList.length > 0">
            <div class="classroom" @click="goSeeClassroom(classroomList[0].url)">
              <div class="classroom-title">
                <img src="http://img30.360buyimg.com/jr_image/jfs/t1/49963/40/3455/1976/5d146647E706d0cbb/be0545f28ab8bd75.png" alt="" class="title-icon"><span class="title-text">{{classroomList[0].title}}</span>
              </div>
              <div class="classroom-info common-font">{{classroomList[0].info}}</div>
              <div class="classroom-more common-font" clstag="pageclick|keycount|new_XQY_md|D4" @click="goSeeClassroom(classroomList[0].url)">立即查看</div>
            </div>
          </div>

          <!-- 预约banner -->
          <div class="reservation-img" v-if="supportReservation" @click="onReservation" clstag="jr|keycount|new_SPXQ_md|Y1">
            <img :src="reservationImg">
          </div>

          <!--附加险-->
          <div class="insurance-additional-area" v-if="productAbleAddItemList.length > 0">
            <h3 class="title">附加险(可选)</h3>
            <ul>
              <li class="insurance-additional-list" v-for="(item,index) in productAbleAddItemList" :key="index">
                <div class="insurance-additional-top">
                  <div class="check-box">
                    <input type="checkbox" class="check-btn" @click="selectCompareItem(index,item)">
                  </div>

                  <div class="infor-additional-box" @click="toggleFJXPlans(index)">
                    <h3>{{item.name}}</h3>
                    <p>{{item.subTitle}}</p>
                  </div>

                  <div class="up-price-additional"><em v-if="item.price.value !=='' && item.price.value !== undefined">{{item.price.value}}</em><i>{{item.price.unitName}}</i>起
                  </div>
                </div>
                <div class="insurance-additional-bottom"
                     v-show="(item.plans !==undefined && item.plans.length > 0 ) && item.show">
                        <span
                          :class="fjxCheckedPlan==subIndex?'fjxCheckedPlan':''"
                          v-for="(subItem,subIndex) in item.plans"
                          @click="FJXCheckStartBuy(subIndex,subItem.price.value,index)"
                          :key="subIndex"
                        >
                          {{subItem.planName}}
                        </span>
                </div>
              </li>
              <!--<li class="insurance-additional-list">-->
              <!--<div class="insurance-additional-top">-->
              <!--<div class="infor-additional-box">-->
              <!--<h3>恶性肿瘤赴日医疗保险金</h3>-->
              <!--<p><span>综合意外首选</span><i></i><span>月月领钱</span></p>-->
              <!--</div>-->
              <!--<div class="check-box"></div>-->
              <!--<div class="up-price-additional"><i>￥</i><em>48</em>起</div>-->
              <!--</div>-->
              <!--<div class="insurance-additional-bottom">-->
              <!--<span>98元起</span>-->
              <!--<span>198元起</span>-->
              <!--<span>998元起</span>-->
              <!--</div>-->
              <!--</li>-->
            </ul>
          </div>

        </div>
        <!--绿色通道-->
        <div class="withicon-insurance-link" v-if="productIntroduce.staticInfo.green">
          <div class="withicon-insurance-box" @click="toggleJytdText()">
            <div class="img-box lstd-icon"></div>
            <p>{{productIntroduce.staticInfo.green.title}}</p>
            <i class="down-sj" :class="jytdText?'up-sj':''"></i>
          </div>
          <div class="withicon-insurance-con" v-if="jytdText">
            <p v-html="productIntroduce.staticInfo.green.info"></p>
          </div>
        </div>
        <!--小贴士-->
        <div class="withicon-insurance-link" v-if="productIntroduce.staticInfo.text"
             @click="goRealPage(productIntroduce.staticInfo.text.code)">
          <div class="img-box xts-icon"></div>
          <p>{{productIntroduce.staticInfo.text.title}}</p>
          <i class="link-sj"></i>
        </div>
        <!-- 专属服务 -->
        <div class="withicon-insurance-link" v-if="productIntroduce.staticInfo.exclusive"
             @click="goRealPage(productIntroduce.staticInfo.exclusive.code)">
          <div class="img-box i-zsfw"></div>
          <p>{{productIntroduce.staticInfo.exclusive.title}}</p>
          <i class="link-sj"></i>
        </div>
      </div>
      <!--录单区-->
      <!-- <div class="set-insurance-area" v-if="false">
        <div class="set-insurance-box">

        </div>
      </div> -->
      <!--产品介绍区-->
      <div class="product-introduction-area">
        <div class="product-introduction-box">
          <div class="introduction-title-area">

            <div class="introduction-title-con">
              <div class="introduction-title-box clearfix">
                <div class="title-item" :class="activeItem == index ? 'active-item':''"
                     v-for="(item,index) in proTitleList" @click="goAnchor('#anchor-'+index, index)" :key="index">{{item}}
                  <div class="bottom-line"></div>
                </div>
              </div>
            </div>
          </div>
          <div class="introduction-img-box" id="anchor-0">
            <div v-for="(item,index) in productIntroduce.introduces" :key="index" v-html="item.content">

            </div>
            <!--<img src="//img30.360buyimg.com/jr_image/jfs/t1/15489/1/8501/616814/5c78a4a6Ece1be2b4/e0d3e8aa45810a82.png" alt="">-->
          </div>
          <div class="introduction-infor-con">
            <div class="introduction-infor-box" id="anchor-1">
              <div class="title">购买须知</div>
              <div class="introduction-infor-main">
                <div class="p-list" v-for="(item,index) in productIntroduce.notices" :key="index" v-html="item.content">
                  <div v-html="item.content"></div>
                </div>
                <!--2. 可购买份数：1份；</br>-->
                <!--3. 本产品首次投保具有30天等待期，连续投保及因意外导致的保险责任无等待期；</br>4、续保规则：被保险人续保时不再设等待期，但若续保的方案保额超过上一保单年度，则超出部分的保额需重新计算等待期；</br>5、投保前，须阅读并了解本保险各分计划对应的适用保险条款，请务必阅读其中的责任免除部分；</div>-->

              </div>
            </div>

            <div class="introduction-infor-box" id="anchor-2">
              <div class="title">理赔流程 <span class="claims-now" v-if="productIntroduce.staticInfo.claim"
                                            @click="goRealPage(productIntroduce.staticInfo.claim.code)">{{productIntroduce.staticInfo.claim.title}}</span>
              </div>
              <div class="introduction-infor-main">
                <div class="process-list-box">
                  <div class="process-list-con" v-for="(item,index) in productIntroduce.claimProcess" :key="index">
                    <h2>{{item.title}}</h2>
                    <div class="process-content" v-html="item.content"></div>
                    <div class="process-circle">{{index+1}}</div>
                    <div class="line" v-if="index < productIntroduce.claimProcess.length-1"></div>
                  </div>
                </div>
              </div>
            </div>

            <div class="introduction-infor-box" id="anchor-3">
              <div class="title">常见问题</div>
              <div class="introduction-infor-main">
                <div class="process-list-box">
                  <div class="process-list-con question-list-con" v-for="(item,index) in productIntroduce.commonProblem"
                       :key="index">
                    <h2>{{item.title}}</h2>
                    <p v-html="item.content"></p>
                    <div class="process-circle">Q{{index+1}}</div>
                  </div>
                </div>
              </div>
              <div class="more-question" v-if="clauses.base.length > 0">更多问题请参考
                <a
                  href="javascript:;"
                  v-for="(item,index) in clauses.base"
                  :key="index"
                  @click="checkClause(item.value,item.display)"
                >
                  《{{item.display}}》
                </a>
                <a
                  href="javascript:;"
                  v-for="(item,index) in clauses.common"
                  :key="'s'+index"
                  @click="checkClause(item.value,item.display)"
                >
                  《{{item.display}}》
                </a>
                <!--<a href="javascript:;" v-for="(item,index) in clauses.base" :key="index">《{{item.display}}》</a>-->
              </div>
            </div>
          </div>
        </div>
      </div>
      <!--真实案例-->
      <div class="withicon-insurance-link mt20Rem mb20rem" v-if="productIntroduce.staticInfo.real"
           @click="goRealPage(productIntroduce.staticInfo.real.code)">
        <div class="img-box lpal-icon"></div>
        <p>{{productIntroduce.staticInfo.real.title}}</p>
        <i class="link-sj"></i>
      </div>

      <!-- <div class="bottom-arguments-area" v-if="false">
        <div class="bottom-arguments-box">
          <span class="arguments-check-box arguments-checked"></span>
          <p>本人已充分理解并同意
            <a
              href="javascript:;"
              v-for="(item,index) in productClauses.base"
              :key="index"
              @click="checkClause(item.value,item.display)"
            >
              《{{item.display}}》
            </a>
            <a
              href="javascript:;"
              v-for="(item,index) in productClauses.common"
              :key="'s'+index"
              @click="checkClause(item.value,item.display)"
            >
              《{{item.display}}》
            </a>
            的全部内容</p>
        </div>
      </div> -->

      <bottom-fixedbtn :custom="!(productRule.plusExclusive && typeof applicant.plus !== 'undefined' && !applicant.plus)" :has-contact="baseInfor.needService" :has-shop="footerBtnData.isjmi" :product="footerBtnData">

        <div class="flexitem1 flexbox" slot='custom'>
          <div class="insurance-money">
            <span class="label-price">金额</span>
            <!--首月1元-->
            <div class="insurance-money-b" v-if="insuranceClassFlag.firstNFlag">
              <a>首月{{baseInfor.price.value}} {{baseInfor.price.unitName}}</a>
              <p>次月起{{totalMoney}}{{baseInfor.price.unitName}}/月</p>
              <!--<p>次月起{{baseInfor.originalPrice.value}}{{baseInfor.price.unitName}}/月</p>-->
            </div>
            <!--非首月1元-->
            <b v-if="!insuranceClassFlag.firstNFlag">{{totalMoney}}</b>
            <em v-if="!insuranceClassFlag.firstNFlag">{{baseInfor.price.unitName}}</em>
          </div>
          <div class="buy-insurance-btn" :class="{'expire-insurance': saleStatus}">
            <a
              href="javascript:;"
              clstag="jr|keycount|new_SPXQ_md|C1"
              @click="goSetInsurance()"
            >{{goInsuranceTxt}}</a>
          </div>
        </div>

      </bottom-fixedbtn>

      <!--保费试算-->
      <transition name="fadeInUp">
        <div class="insurace-try-calculation" v-if="isShowCalculation">
          <div class="try-calculation-box">
            <h3 class="title">
              <span>保费试算</span>
              <em class="sub-title">保费与被保人年龄、有无社保等条件相关</em>
              <i class="close-icon" @click="showCalculation"></i>
            </h3>
            <div class="try-calculation-win">
             <Scroller
              ref="scroll"
              :auto-update="true"
              :pullUp="false"
              :pullDown="false"
              class="">
                <ul class="try-calculation-ul">
                  <li class="try-calculation-list" v-if="payFrequencies.length>0">
                    <div class="key">
                      <p>缴费频率</p>
                    </div>
                    <div class="val">
<!--
                      <slider-items :sliderItem='payFrequencies' :defaultValues="payFrequenciesSelectIdx"
                                    @checkedItemIdx='itemCheckedPayFrequencies'></slider-items>
-->
                      <slider-itemC :sliderList='payFrequencies'
                                    :defaultValues="payFrequenciesSelectIdx"
                                    @checkedItemIdx='itemCheckedPayFrequencies'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="payPeriods.length>0">
                    <div class="key">
                      <p>缴费年限</p>
                    </div>
                    <div class="val">
<!--                      <slider-items :sliderItem='payPeriods' :defaultValues="payPeriodsSelectIdx"
                                    @checkedItemIdx='itemCheckedPayPeriods'></slider-items>-->
                      <slider-itemC :sliderList='payPeriods'
                                    :defaultValues="payPeriodsSelectIdx"
                                    @checkedItemIdx='itemCheckedPayPeriods'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="insurancePeriods.length>0">
                    <div class="key">
                      <p>保障期间</p>
                    </div>
                    <div class="val">
<!--
                      <slider-items :sliderItem='insurancePeriods' :defaultValues="insurancePeriodsSelectIdx"
                                    :slidesPerView='2' @checkedItemIdx='itemCheckedInsurancePeriods'></slider-items>
-->
                      <slider-itemC :sliderList='insurancePeriods'
                                    :defaultValues="insurancePeriodsSelectIdx"
                                    @checkedItemIdx='itemCheckedInsurancePeriods'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="amounts.length>0">
                    <div class="key">
                      <p>保额列表</p>
                    </div>
                    <div class="val">
<!--                      <slider-items :sliderItem='amounts' :defaultValues="amountsSelectIdx"
                                    @checkedItemIdx='itemCheckedAmounts'></slider-items>-->
                      <slider-itemC :sliderList='amounts'
                                    :defaultValues="amountsSelectIdx"
                                    @checkedItemIdx='itemCheckedAmounts'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="socialSecurities.length>0">
                    <div class="key">
                      <p>社保列表</p>
                    </div>
                    <div class="val">
<!--
                      <slider-items :sliderItem='socialSecurities' :defaultValues="socialSecuritiesSelectIdx"
                                    @checkedItemIdx='itemCheckedSocialSecurities'></slider-items>
-->
                      <slider-itemC :sliderList='socialSecurities'
                                    :defaultValues="socialSecuritiesSelectIdx"
                                    @checkedItemIdx='itemCheckedSocialSecurities'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="genderList.length>0">
                    <div class="key">
                      <p>投保性别</p>
                    </div>
                    <div class="val">
<!--                      <slider-items :sliderItem='genderList' :defaultValues="genderListSelectIdx"
                                    @checkedItemIdx='itemCheckedGenderList'></slider-items>-->
                      <slider-itemC :sliderList='genderList'
                                    :defaultValues="genderListSelectIdx"
                                    @checkedItemIdx='itemCheckedGenderList'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="smokedList && smokedList.length>0">
                    <div class="key">
                      <p>有无吸烟史</p>
                    </div>
                    <div class="val">
<!--                      <slider-items :sliderItem='smokedList' :defaultValues="smokedListSelectIdx"
                                    @checkedItemIdx='itemCheckedSmokedList'></slider-items>-->
                      <slider-itemC :sliderList='smokedList'
                                    :defaultValues="smokedListSelectIdx"
                                    @checkedItemIdx='itemCheckedSmokedList'></slider-itemC>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="productRule.insuredAgeLimit.needAge">
                    <div class="key">
                      <p>出生日期</p>
                    </div>
                    <div class="val">
                      <div class="go-choose-box" @click="showPlugin">
                        <span :class="dateVal !=='请选择被保人出生日期' ? 'colFour':''">{{dateVal}}</span>
                        <i class="sj-icon"></i>
                      </div>
                    </div>
                  </li>
                  <li class="try-calculation-list" v-if="productAbleAddItemList.length > 0">
                    <div class="key">
                      <p>附加险名称</p>
                    </div>
                    <div class="val">
                      <slider-itemM
                        :sliderItem='productAbleAddItemList'
                        :mutiple="true"
                        @checkedItemIdx="checkedItemFjx"
                      ></slider-itemM>
                    </div>
                  </li>
                  <li
                    class="try-calculation-list"
                    v-show="JSON.stringify(checkedItemFjxStartBuy) !== '{}'"
                    v-for="(item, index) in checkedItemFjxStartBuy" :key="index"
                  >
                    <div class="key">
                      <p>{{item.name}}</p>
                    </div>
                    <div class="val">
                      <!--<slider-itemF :sliderItem='item.plans' :defaultValues="0"></slider-itemF>-->
                      <slider-itemC :sliderList='item.plans'
                                    :defaultValues="0"
                                    :display-key="'fjx'"
                                    ></slider-itemC>
                    </div>
                  </li>
                  <!-- <li
                    class="try-calculation-list"
                    v-if="false"
                    v-show="checkedItemFjxStartBuy.length > 0"
                  >
                    <div class="key">
                      <p>附加险金额</p>
                    </div>
                    <div class="val">
                      <slider-itemF :sliderItem='checkedItemFjxStartBuy'></slider-itemF>
                    </div>
                  </li> -->

                </ul>
            </Scroller>
            </div>
          </div>
        </div>
      </transition>
      <transition name="opacityIn">
        <div class="windowMask" v-if="isShowCalculation" @click="showCalculation"></div>
      </transition>
      <select-item
        :isShow = "isShowGeneSelectPro"
        :selectedItem = "geneSelectedItem"
        :selectListData = "geneSelectListData"
        @selectItem="selectGeneItem"
        @close = 'closeGeneSelectPro'
      >

      </select-item>
      <div class="windowMask" v-if="isShowGeneSelectPro" @click="closeGeneSelectPro()"></div>
    </div>
    <!--基因检测详情页-->
    <!--受益人说明弹窗-->
    <full-panel title="领取方式" @closeFullPanel="closeFullPanel" :isShowFullPanel="isShowFullPanelReceivAge">
      <p>您可以选择按年或者按月的方式领取</p>
    </full-panel>
    <full-panel title="领取年龄" @closeFullPanel="closeFullPanel" :isShowFullPanel="isShowReceivWay">
      <p>您可以选择从60周岁领取或65周岁领取</p>
    </full-panel>
    
  </div>
</template>

<script>
// import sliderItems from '../../components/sliderItem'
import sliderItemM from '../../components/sliderItemM'
import sliderItemC from '../../components/sliderItemC'
// import sliderItemF from '../../components/sliderItemF'
import selectItem from '../../components/selectItem'
import {Datetime} from 'vux'
import {fetch} from '../../api/fetch.js'
import {apiUrl, proParam} from '../../api/api.js'
import $ from 'jquery'
import {timestampToTime, setSession, noScroll, canScroll , formatMoney} from '../../tools/utils'
import share from '../../tools/share'
import moment from 'moment'
import toast from '../../components/toast/toast.js'
import Scroller from 'vue-slim-better-scroll'
import BottomFixedbtn from '../../components/BottomFixedbtn.vue'
import pickerItem from '../../components/picker/picker'
import { constants } from 'crypto';
import { debug } from 'util';
import fullPanel from "../../components/comFullPanel";

export default {
  name: 'proDetails',
  components: {
    // sliderItems,
    Datetime,
    // sliderItemF,
    sliderItemC,
    sliderItemM,
    Scroller,
    BottomFixedbtn,
    selectItem,
    pickerItem,
    fullPanel
  },
  created () {
    this.getProDetailsInfo()
    let docBody = document.body || document.documentElement
    setTimeout(() => {
      docBody.scrollTop = docBody.scrollTop + 1
    }, 10)

    // 清空 缓存数据
    window.sessionStorage && sessionStorage.clear()
  },
  // mounted() {
  // },
  // beforeCreate() {
  //   document.querySelector('body').setAttribute('style', 'background:#f5f5f5')
  // },
  destroyed () {
    // document.querySelector('body').setAttribute('style', '');
    canScroll()
    window.removeEventListener('scroll', this.onScrollHanlder)

    // 重置tab index
    this.activeItem = 0
  },
  data () {
    return {
      insuranceClassFlag: '',
      geneFlagDetails: false,
      productId: this.$route.query.productId,
      /* 底部最初值 */
      beginMoney: '',
      /* 投保计划已选金额 */
      activePlansMoney: '',
      checkbox: [],
      /* 附加险方案底部默认值 */
      fjxCheckedPlan: 0,
      /* 附加险方案底部联动值 */
      fjxCheckedPlanMoney: '',
      fjxChecked: 0,
      fjxCheckedFlag: true,
      // prevClickNum: 0,

      jytdText: false,
      activePlansItem: 0,
      activePlansAmount: 0,
      plansNum: '',
      prodetailsData: '',
      productBase: {
        name: '',
        price: {
          value: 0
        },
        originalPrice: {
          value: 0
        },
        company: {
          name: ''
        }
      },
      /* 返回的基本信息 */
      baseInfor: {
        name: '',
        company: '',
        price: '',
        serviceUrl: ''
      },
      /* 协议 */
      clauses: {
        base: [],
        common: []
      },
      productPlans: [],
      productIntroduce: {
        claimProcess: [],
        commonProblem: [],
        introduces: [],
        notices: [],
        staticInfo: {
          claim: '',
          green: '',
          real: '',
          text: ''
        }
      },
      productLiabilities: {},
      productAbleAddItemList: [],
      productCompanyName: '',
      // productPage: {
      //   h5Introduce: ''
      // },
      productRule: {
        insuredAgeLimit: {},
        premiumLimit:[] //保险限制
      },
      premiumLimit : { //保费限制
        tryCalDate : [],
        tryCalModel : [],
        tryCalNumDate : [],
        tryCalNumModel : []
      },
      receivAge:[], //渤海年金 领取年龄
      receivAgeList:{},      
      receivAgeSelectIdx: 0 ,////
      receivAgeDefaultValues:'',////
      receivAgeShow:'',
      receivAgeUnit:'',////
      receivAgeDate:'',////
      receiveCycle:[], //渤海年金 领取方式 
      receiveCycleSelectIdx: 0,////
      receiveCycleShow:'',
      receiveCycleDefaultValues:'',////
      receiveCycleDate:'',////
      receiveCycleVal:'',////
      paymentWay:"",////缴费方式
      sumNianjinPrice:"", ////累计可领取金额
      isOnePrice:"",////一次性可领取金额
      yearOrMonthPrice:"",////每月可领取养老金
      totalPremium:"", ////总保费,
      priceNumbh:"", ////领取金额
      isShowFullPanelReceivAge:false, //领取年龄弹框
      isShowReceivWay:false,
      applicant: {},
      // flag: true,
      // beginMoneyFlag: true,
      /* 产品介绍list */
      proTitleList: ['产品介绍', '购买须知 ', '理赔流程 ', '常见问题'],
      /* 产品介绍list默认选中 */
      activeItem: 0,
      /* 展示保费试算 */
      isShowCalculation: false,
      /* 缴费频率 */
      payFrequencies: [],
      // payFrequenciesDefaultValues:0,
      payFrequenciesSelectIdx: 0,
      /* 缴费年限 */
      payPeriods: [],
      // payPeriodsDefaultValues:0,
      payPeriodsSelectIdx: 0,
      /* 保障期间 */
      insurancePeriods: [],
      insurancePeriodsDefaultValues: '',
      insurancePeriodsSelectIdx: 0,
      /* 保额列表 */
      amounts: [],
      amountsDefaultValues: '',
      amountsSelectIdx: 0,
      amountsFlag: '',
      // 方案索引
      plansSelectIdx: 0,
      /* 社保列表 */
      socialSecurities: [],
      socialSecuritiesDefaultValues: '',
      socialSecuritiesSelectIdx: 0,
      /* 投保性别列表 */
      genderList: [],
      smokedList: [],
      smokedListSelectIdx: 0,
      genderListDefaultValues: '',
      genderListSelectIdx: 0,
      smokedListDefaultValues: '',
      fjxSelectedIndex: [],
      trial: '',
      limitYear: ['3年', '5年', '10年', '20年', '130年'],
      checkedlimitYear: '',

      // fjxName: ['重疾保险金', '恶性肿瘤保险金'],
      // fjxMoney: ['10万', '20万', '50万'],
      dateVal: '请选择被保人出生日期',
      /* 附加险右侧金额数组 */
      fjxTotalPrice: 0,
      /* 产品标志 */
      goFlag: {
        // 赠险
        giveFlag: false,
        // 续期产品
        renewalFlag: false,
        // 返还型产品
        returnFlag: false,
        // 万能险
        powerfulFlag: false,
        // 宠物险
        petFlag: false,
        // 设备险
        equipmentFlag: false,
        // 提额赠险-京宝贝
        addAmountFlag: false,
        // 首期N元
        firstNFlag: false,
        // 基因检测
        geneFlag: false,
        // 家财险
        houseFlag: false,
        //渤海年金
        bohaiLifeFlag: false
      },
      checkedItemFjxStartBuy: {},
      // 是否是服务类订单---如果是服务类订单，用trial.plans关联，否则用amonts
      isServerOrder: false,

      // 以下是保费试算所需参数
      // 主险
      // productCodeOnline: '',
      priceNum: '',
      buyNumber: '1',
      // 有附加险
      // mainProductCode: '',
      // otherProduct: '',
      // SumProductPriceSum: '',
      // zhuxianbenbaoe: '',
      // zhuxianbaofei: '',
      // 公共参数
      // 保障期间
      insureDate: '',
      paymentDate: '',
      // insurePlan: '',
      // socailSecurityOnline: '',
      sex: '1',
      age: '',
      /* 是否经过试算 */
      initCalcu: false,
      sumPrice: '0',
      // 产品下架信息
      saleStatus: false,
      saleInfo: '',
      trialShow: true,
      // 试算 入参 产品列表
      calcProList: [],
      // 保障期间
      insureDateUnit: '',
      // 缴费期间
      payPeriodsUnit: '',
      // 保障方案需要
      partnerItemId: '',
      goInsuranceTxt: '立即投保',
      isNewOrder: '', //是否新单
      itemId: '',
      // isjmi: proParam.jdmall,
      footerBtnData: {
        isjmi: proParam.jdmall,
        itemId: '',
        vendorId: '',
        name: '',
        footBtnFill: false,
        footBtnFillText: ''
      },
      isShowGeneSelectPro: false,
      geneSelectListData: [],
      geneSelectedItem: 0,
      hasShowSelectList: false,
      supportReservation: false,
      reservationImg: '//storage.360buyimg.com/public-static-resource/insurance/images/yuyue_banner.png',
      activityType: '', // 活动编码 预约用
      classroomList: [] // 教育楼层
    }
  },
  methods: {
    transformMoney(num){
      return formatMoney(num)
    },
    // 预约
    onReservation () {
      let productInfo = {productCode: this.itemId, productName: encodeURIComponent(this.productBase.name), activityType: this.activityType}
      setSession('productInfo', JSON.stringify(productInfo))

      this.$router.push({name: 'reservation', query: productInfo})
    },
    /* 跳转至保障详情页 */
    goGuaranteeDetails () {
      this.$router.push({path: 'guaranteeDetails', query: {productId: this.productId, itemId: this.itemId}})
    },
    /* 跳转至录单页 */
    goSetInsurance () {
      if (this.saleStatus) {
        toast({message: this.saleInfo})
        return
      }
      // 存入已选中索引
      // 缴费频率
      setSession('payFrequenciesSelectIdx', this.payFrequenciesSelectIdx)
      // 缴费年限
      setSession('payPeriodsSelectIdx', this.payPeriodsSelectIdx)
      // 保障期间
      setSession('insurancePeriodsSelectIdx', this.insurancePeriodsSelectIdx)
      // 保额列表
      setSession('amountsSelectIdx', this.amountsSelectIdx)
      setSession('amountsSelectVal', this.amountsDefaultValues)
      // 社保列表
      setSession('socialSecuritiesSelectIdx', this.socialSecuritiesSelectIdx)
      // 投保性别列表
      setSession('genderListSelectIdx', this.genderListSelectIdx)
      // 投保性别列表
      setSession('totalMoney', this.totalMoney)
      // 附加险  fjxSelectedIndex
      setSession('fjxSelectedIndex', JSON.stringify(this.fjxSelectedIndex))
      // flag 服务类
      setSession('amountsFlag', this.amountsFlag)
      // partnerItemId
      setSession('partnerItemId', this.partnerItemId)
      // 领取年龄
      setSession('receivAgeSelectIdx', this.receivAgeSelectIdx)
      // 领取方式
      setSession('receiveCycleSelectIdx', this.receiveCycleSelectIdx)

      let goPath = ''
      // let goFlag = '';
      if (typeof (this.goFlag.geneFlag) !== 'undefined' && this.goFlag.geneFlag) {
        // 基因检测--单独的录单页
        // goPath = 'setInsuranceGene';
        if (this.hasShowSelectList) {
          this.$router.push({path: 'setInsuranceGene', query: Object.assign({}, {productId: this.productId, itemId: this.itemId}, this.$route.query)})
        }
        this.showGeneSelectPro()
        return
        // goFlag = 'geneFlag'
      }
      /* 跳至宠物录单 */
      if (typeof (this.goFlag.petFlag) !== 'undefined' && this.goFlag.petFlag) {
        // 宠物险
        goPath = 'setInsurancePet'
        // goFlag = 'petFlag'
      } else if (typeof (this.goFlag.addAmountFlag) !== 'undefined' && this.goFlag.addAmountFlag) {
        // 提额赠险-京宝贝（如果被保人生日与生效日期同一天，则生效日期向前提一天,即时间戳-86400000）
        goPath = 'setInsuranceBaby'
        // goFlag = 'addAmountFlag'
      } else if (typeof (this.goFlag.returnFlag) !== 'undefined' && this.goFlag.returnFlag) {
        // this.goFlag.returnFlag 返还型产品--多一个返还金，试算结果不在底部按钮区。底部按钮区金额固定
        goPath = 'setInsuranceYear'
      } else if(typeof (this.goFlag.bohaiLifeFlag) !== 'undefined' && this.goFlag.bohaiLifeFlag) {
        goPath = 'setInsuranceBoHaiYear'
      } else {
        // this.goFlag.giveFlag 赠险--底部按钮不同
        // this.goFlag.renewalFlag 续期产品--多一个续期模块
        // this.goFlag.powerfulFlag 万能险--中融年金（多个万能险及万能险保费两行字段，需与产品确认该险种是否下架及下架时间）
        // this.goFlag.equipmentFlag 设备险--多个设备选择及机型选择（上下联动接口）
        // this.goFlag.firstNFlag 首期N元
        // this.goFlag.houseFlag 家财险--普通，多两列被保人选择房屋地址
        goPath = 'setInsuranceOne'
        // goFlag = (this.goFlag.giveFlag && 'giveFlag') || (this.goFlag.renewalFlag && 'renewalFlag') || (this.goFlag.returnFlag && 'returnFlag') || (this.goFlag.powerfulFlag && 'powerfulFlag') || (this.goFlag.equipmentFlag && 'equipmentFlag') || (this.goFlag.firstNFlag && 'firstNFlag') || (this.goFlag.houseFlag && 'houseFlag')
      }

      this.$router.push({path: goPath, query: Object.assign({}, {productId: this.productId, itemId: this.itemId}, this.$route.query)})
    },
    reIsShow (index) {
      return this.isShowAbleAddplans[index]
    },
    showGeneSelectPro () {
      this.isShowGeneSelectPro = true
      this.hasShowSelectList = true
    },
    closeGeneSelectPro () {
      this.isShowGeneSelectPro = false
      this.hasShowSelectList = false
    },
    selectGeneItem (item, index) {
      this.geneSelectedItem = index
      setSession('selectedIndex', index)
      // this.hasShowSelectList = true;
    },
    /* 调用试算接口 */
    async tryCalculation () {
      this.initCalcu = true
      let _this = this
      let list = await this.getCalcParams()
      await fetch.post({
        url: apiUrl.calculateProductRatePriceForOnline,
        data: {
          'list': list
        }
      }, (res) => {
        if (res.code === '000000') {
          _this.sumPrice = res.value.sumPrice
          _this.sumNianjinPrice = res.value.sumNianjinPrice
          _this.isOnePrice = res.value.isOnePrice
          _this.yearOrMonthPrice = res.value.yearOrMonthPrice
          _this.totalPremium = res.value.totalPremium
        } else {
          toast({message: res.message || '试算失败请稍后重试或联系客服[错误：' + res.code + ']'})
        }
      })
    },
    /* 切换产品方案 */
    plansActive (index, amount, pansMoney, item) {
      this.activePlansItem = index
      this.activePlansAmount = amount
      this.activePlansMoney = pansMoney
      this.priceNum = amount
      this.amountsSelectIdx = index
      this.amountsDefaultValues = item.otherValue
      this.initCalcu = false
      this.amountsFlag = item.flag
      this.partnerItemId = item.partnerItemId
    },
    // 已选保额列表
    itemCheckedAmounts (item, idx, val) {
      this.plansActive(idx, val, val, item)
      this.amountsSelectIdx = idx
      this.amountsDefaultValues = item.otherValue
      this.tryCalculation()
    },
    // 已选性别列表
    itemCheckedGenderList (item, idx, val) {
      this.genderListSelectIdx = idx
      this.genderListDefaultValues = val
      this.tryCalculation()
    },
    // 已选吸烟
    itemCheckedSmokedList (item, idx, val) {
      this.smokedListSelectIdx = idx
      this.smokedListDefaultValues = val
    },
    // 已选缴费频率
    itemCheckedPayFrequencies (item, idx, val) {
      this.payFrequenciesSelectIdx = idx
      this.payFrequenciesDefaultValues = val
      this.tryCalculation()
    },
    // 已选缴费年限
    itemCheckedPayPeriods (item, idx, val) {
      this.payPeriodsSelectIdx = idx
      this.payPeriodsDefaultValues = val
      this.payPeriodsUnit = item.otherType
      this.paymentDate = item.otherValue
      this.tryCalculation()
    },
    // 已选领取年龄 渤海年金
    itemCheckedreceivAgePeriods (item, idx, val) {
      this.receivAgeSelectIdx = idx
      this.receivAgeDefaultValues = val
      this.receivAgeUnit = item.otherType
      this.receivAgeDate = item.otherValue
      this.receivAgeShow = item.display
      console.log(this.receivAgeDefaultValues,this.receivAgeDate)
      this.tryCalculation()
    },
    // 已选领取方式 渤海年金
    itemCheckedreceiveCyclePeriods (item, idx, val) {
      this.receiveCycleSelectIdx = idx
      this.receiveCycleDefaultValues = val
      this.receiveCycleDate = item.display
      this.receiveCycleVal = item.value
      this.receiveCycleShow = item.display.charAt(1)
      this.tryCalculation()
    },
    // 已选保障期间
    itemCheckedInsurancePeriods (item, idx, val) {
      this.insurancePeriodsSelectIdx = idx
      this.insurancePeriodsDefaultValues = item.otherValue
      this.insureDateUnit = item.otherType
      this.tryCalculation()
    },
    // 已选社保列表
    itemCheckedSocialSecurities (item, idx, val) {
      this.socialSecuritiesSelectIdx = idx
      this.socialSecuritiesDefaultValues = val
      this.tryCalculation()
    },
    // 选择附加险
    selectCompareItem (index, item) {
      let num = 0
      let fjxTotalPrice = 0
      let btn = document.getElementsByClassName('check-btn')
      for (let i = 0; i < btn.length; i++) {
        let price = this.productAbleAddItemList[i].price.value
        if (btn[i].checked) {
          fjxTotalPrice += Number(price)
        }
      }
      if (btn[index].checked) {
        this.fjxSelectedIndex.push(index)
      } else {
        for (let k = 0; k < this.fjxSelectedIndex.length; k++) {
          let item = this.fjxSelectedIndex[k]
          if (item === index) {
            this.fjxSelectedIndex.splice(item, 1)
          }
        }
      }
      this.fjxTotalPrice = fjxTotalPrice
    },
    /* 附加险选择起购金 */
    FJXCheckStartBuy (index, money, oneIndex) {
      let _this = this
      this.fjxCheckedPlan = index
      this.fjxCheckedPlanMoney = money
      this.productAbleAddItemList[oneIndex].price.value = money
      // this.productAbleAddItemList.map(function (item) {
      //     _this.$set(item.price,'value',money)
      //     // console.log(item.price.value)
      // })
      let fjxTotalPrice = 0
      let btn = document.getElementsByClassName('check-btn')
      for (let i = 0; i < btn.length; i++) {
        let price = this.productAbleAddItemList[i].price.value
        if (btn[i].checked) {
          fjxTotalPrice += Number(price)
        }
      }
      this.fjxTotalPrice = fjxTotalPrice
    },
    /* 单选 */
    checkRadio (index) {
      this.fjxChecked = index
      this.fjxCheckedFlag = !this.fjxCheckedFlag
    },
    /* 多选 */
    check (i) {
      var idx = this.checkbox.indexOf(i)
      // 如果已经选中了，那就取消选中，如果没有，则选中
      if (idx > -1) {
        this.checkbox.splice(idx, 1)
      } else {
        this.checkbox.push(i)
      }
      console.log(this.checkbox)
    },
    // 跳转条款详情
    checkClause (id, title) {
      let query = {
        id
        // title: encodeURIComponent(title)
      }
      // 预先设置 跳转后 title
      for (let routes of this.$router.options.routes) {
        if (routes.name === 'insuranceClause') {
          routes.meta.title = title
        }
      }

      //
      this.$router.push({path: 'insuranceClause', query})
    },
    /* 点击保费试算中的附加险选项 */
    checkedItemFjx (item, idx, val, event) {
      let _this = this
      console.log('item', item)

      if (item.isSelected && !this.checkedItemFjxStartBuy[idx.toString()]) { // 选中以后
        this.$set(this.checkedItemFjxStartBuy, idx.toString(), item)
        this.calcProList.push(item.itemId)
      } else if (!item.isSelected && this.checkedItemFjxStartBuy[idx.toString()]) {
        delete this.checkedItemFjxStartBuy[idx.toString()]
        for (let i = 0; i < this.calcProList.length; i++) {
          let id = this.calcProList[i]
          if (id === item.itemId) {
            this.calcProList.splice(i, 1)
          }
        }
      }

      // 因高度改变 刷新 scroll
      this.$refs.scroll.refresh()
      // if (item.plans) {
      //   item.plans.map(function (sItem, index) {
      //     if (sItem.price.unitName && sItem.price.value !== '') {
      //       _this.checkedItemFjxStartBuy.push(sItem.planName);
      //     } else if (sItem.price.value === '') {
      //       return _this.checkedItemFjxStartBuy = []
      //     } else {
      //       return _this.checkedItemFjxStartBuy.push(sItem.price.value)
      //     }
      //   })
      // } else {
      //   this.checkedItemFjxStartBuy = []
      // }
      // let {itemId} = item;
      // let hasSelected = event.srcElement.parentNode.className.indexOf('swiper-slide-now');
      // if (hasSelected > -1) {
      //   // 选中
      //   this.calcProList.push(itemId)
      // } else {
      //   for (let i = 0; i < this.calcProList.length; i++) {
      //     let id = this.calcProList[i];
      //     if (itemId == id) {
      //       this.calcProList.splice(i, 1);
      //     }
      //   }
      // }
      this.tryCalculation()
    },
    async getProDetailsInfo () {
      let _this = this
      let activityOrderId = this.$route.query.activityOrderId
      let activityPolicyId = this.$route.query.activityPolicyId
      await fetch.get({
        url: apiUrl.msProduct,
        data: {
          itemId: this.productId,
          renewal: this.$route.query.renewal,
          repurchase: this.$route.query.repurchase,
          orderId: this.$route.query.orderId,
          policyNo: this.$route.query.policyNo,
          sourceNotify: this.$route.query.sourceNotify,
          activityOrderId,
          activityPolicyId,
          merchant: {
            merchantNo: proParam.merchantCode,
            merchantName: proParam.merchantName,
            sceneCode: proParam.sceneCode
          }
        }
      }, (res) => {
        if (res.code == '0000') {
          if (!res.response.saleStatus) {
            _this.saleStatus = true
            _this.saleInfo = res.response.saleInfo
            
          }
          // _this.productId = _this.$route.query.productId;
          _this.itemId = res.response.base.itemId || ''
          // 咨询 和店铺需要参数
          _this.footerBtnData.itemId = res.response.base.itemId
          if (res.jmiShopId) {
            _this.footerBtnData.shopId = res.jmiShopId
          }
          if (res.jmiVenderId) {
            _this.footerBtnData.vendorId = res.jmiVenderId
          }
          if (res.response.base.serviceUrl) {
            _this.footerBtnData.contactUrl = res.response.base.serviceUrl
          }
          if (res.response && res.response.base.name) {
            _this.footerBtnData.name = res.response.base.name
          }
          if (res.response.rule.plusExclusive && typeof res.response.applicant.plus !== 'undefined' && !res.response.applicant.plus) {
            _this.footerBtnData.footBtnFill = true
            _this.footerBtnData.footBtnFillText = '开通PLUS会员后领取'
          }

          // 预约活动编码
          if (res.response.crmActivityCode) {
            _this.activityType = res.response.crmActivityCode
          }
          if (res.response.supportReservation) {
            _this.supportReservation = res.response.supportReservation
          }
          // 教育楼层
          if (res.response && res.response.introduce && res.response.introduce.staticInfo && res.response.introduce.staticInfo.classroom) {
            _this.classroomList = res.response.introduce.staticInfo.classroom
          }

          //
          _this.calcProList[0] = _this.itemId

          if (res.response.isNewOrder) {
            _this.isNewOrder = res.response.isNewOrder
          }

          /* 产品标志 */
          if (res.response.flag) {
            _this.goFlag = res.response.flag
            if (res.response.flag.giveFlag) {
              // _this.goInsuranceTxt = '立即领取';
              _this.goInsuranceTxt = res.response.giveInfo && !res.response.giveInfo.supportGive ? res.response.giveInfo.info : '立即领取'

              _this.saleStatus = !res.response.giveInfo.supportGive
              _this.saleInfo = res.response.giveInfo.info
            }
          }
          if (res.response.flag.serviceFlag) {
            _this.isServerOrder = res.response.flag.serviceFlag
          }
          if (!res.response.flag.needTrial) {
            _this.trialShow = false
          }
          /* 基础信息模块 */
          if (res.response.base) {
            _this.productBase = res.response.base
            _this.baseInfor = res.response.base
          }
          _this.prodetailsData = res.response
          // 产品险种类型
          if (res.response.flag) {
            _this.insuranceClassFlag = res.response.flag
          }
          if (res.response.flag.serviceFlag) {
            _this.isServerOrder = res.response.flag.serviceFlag
          }
          /* 协议 */
          if (res.response.clauses) {
            if (res.response.clauses.base) {
              _this.clauses.base = res.response.clauses.base
            }
            if (res.response.clauses.common) {
              _this.clauses.common = res.response.clauses.common
            }
          }
          /* 投保计划 */
          if (res.response.plans) {
            _this.productPlans = res.response.plans
            if (res.response.plans.length > 0) {
              _this.priceNum = res.response.plans[0].amount
              _this.amountsFlag = res.response.plans[0].flag
            }
          }
          /* 投保规则--年龄 */
          if (res.response.rule) {
            _this.productRule = res.response.rule;
            let receivAgeList = res.response.rule.receivAgeList;
            if(receivAgeList && receivAgeList.receivingAgeList){ //渤海年金 领取年龄
               _this.receivAgeList = receivAgeList;
               _this.receivAge = receivAgeList.receivingAgeList;
               if(_this.receivAge.length === 0){
                 _this.receivAgeDefaultValues = ''
                 _this.receivAgeSelectIdx = 0
                 _this.receivAgeShow = ''
               }else{
                 _this.receivAgeUnit = _this.receivAgeList.receivingType;
                 _this.receivAgeDate = _this.receivAge[0].otherValue
                 _this.receivAgeDefaultValues = _this.receivAge[0].value
                 _this.receivAgeShow = _this.receivAge[0].display
               }
            }
            if(res.response.rule.receiveCycleList){ //渤海年金 领取方式
                _this.receiveCycle = res.response.rule.receiveCycleList;
                if(_this.receiveCycle.length === 0){
                  _this.payPeriodsDefaultValues = ''
                  _this.receiveCycleSelectIdx = 0
                  _this.receiveCycleShow = ''
                  _this.receiveCycleVal = ''
                }else{
                  _this.receiveCycleDate = _this.receiveCycle[0].display
                  _this.receiveCycleVal = _this.receiveCycle[0].value
                  _this.receiveCycleShow = _this.receiveCycle[0].display.charAt(1)
                }
            }
            if(res.response.rule.premiumLimit){
              _this.showPremiumLimit(); //bohai
              _this.getpremiumMsg(1); //bohai
            }
            
          }
          if (res.response.applicant) {
            _this.applicant = res.response.applicant
          }
          if (res.response.introduce) {
            if (res.response.introduce.staticInfo) {
              _this.productIntroduce.staticInfo = res.response.introduce.staticInfo
            }
            if (res.response.introduce.claimProcess) {
              _this.productIntroduce.claimProcess = res.response.introduce.claimProcess
            }
            if (res.response.introduce.commonProblem) {
              _this.productIntroduce.commonProblem = res.response.introduce.commonProblem
            }
            if (res.response.introduce.introduces) {
              _this.productIntroduce.introduces = res.response.introduce.introduces
            }
            if (res.response.introduce.notices) {
              _this.productIntroduce.notices = res.response.introduce.notices
            }
          }
          /* 保障责任 */
          if (res.response.liabilities) {
            _this.productLiabilities = res.response.liabilities
            // for (let key in res.response.liabilities) {
            //   _this.$set(_this.productLiabilities, key, res.response.liabilities[key])
            // }
          }

          /* 附加险 */
          if (res.response.ableAddItemList) {
            _this.productAbleAddItemList = res.response.ableAddItemList
            _this.productAbleAddItemList.map(function (item) {
              _this.$set(item, 'show', false)
              _this.$set(item, 'display', item.name)
            })
          }
          if (_this.productBase.company) {
            _this.productCompanyName = res.response.base.company.simpleName
          }
          /* 保费试算模块 */
          if (res.response.trial) {
            // _this.trial = res.response.trial;
            if (res.response.trial.amounts) {
              _this.amounts = res.response.trial.amounts
              // 如果只有一个数据，则默认选中第一个
              if (_this.amounts.length === 0) {
                _this.amountsDefaultValues = ''
                _this.amountsSelectIdx = 0
              } else {
                _this.amountsDefaultValues = _this.amounts[0].value
              }
            }
            if (res.response.trial.genderList) {
              _this.genderList = res.response.trial.genderList.reverse()
              if (_this.genderList.length === 0) {
                _this.genderListDefaultValues = ''
                _this.genderListSelectIdx = 0
              } else {
                _this.genderListDefaultValues = _this.genderList[0].value
              }
            }
            if (res.response.trial.smokedList && res.response.trial.smokedList.length > 0) {
              _this.smokedList = res.response.trial.smokedList
              _this.smokedListDefaultValues = _this.smokedList[0].value
            }
            if (res.response.trial.payFrequencies) {
              _this.payFrequencies = res.response.trial.payFrequencies
              if (_this.payFrequencies.length === 0) {
                _this.payFrequenciesDefaultValues = ''
                _this.payFrequenciesSelectIdx = 0
              } else {
                _this.payFrequenciesDefaultValues = _this.payFrequencies[0].value
              }
            }
            if (res.response.trial.payPeriods) { //交费期间
              _this.payPeriods = res.response.trial.payPeriods
              if (_this.payPeriods.length === 0) {
                _this.payPeriodsDefaultValues = ''
                _this.payPeriodsSelectIdx = 0
              } else {
                _this.payPeriodsUnit = _this.payPeriods[0].otherType
                _this.paymentDate = _this.payPeriods[0].otherValue
                _this.payPeriodsDefaultValues = _this.payPeriods[0].value
              }
            }
            if (res.response.trial.insurancePeriods) {
              _this.insurancePeriods = res.response.trial.insurancePeriods
              if (_this.insurancePeriods.length === 0) {
                _this.insurancePeriodsDefaultValues = ''
                _this.insurancePeriodsSelectIdx = 0
              } else {
                _this.insureDateUnit = _this.insurancePeriods[0].otherType
                _this.insurancePeriodsDefaultValues = _this.insurancePeriods[0].otherValue
              }
            }
            if (res.response.trial.socialSecurities) {
              _this.socialSecurities = res.response.trial.socialSecurities
              if (_this.socialSecurities.length === 0) {
                _this.socialSecuritiesDefaultValues = '0'
                _this.socialSecuritiesSelectIdx = 0
              } else {
                _this.socialSecuritiesDefaultValues = _this.socialSecurities[0].value
              }
            }
          }
          // 到底是用amounts关联责任列表 还是用plans关联，需要确认
          if (res.response.trial) {
            /* 试算默认值--非服务类 */
            if (res.response.trial.amounts && !_this.isServerOrder) {
              _this.activePlansAmount = res.response.trial.amounts[0].value
            }
          }
          /* 试算默认值--服务类 */
          if (_this.isServerOrder) {
            // _this.activePlansAmount = res.response.plans[0].amount
            _this.activePlansAmount = res.response.plans[0].amount
          }
          if (_this.goFlag.geneFlag) {
            _this.geneFlagDetails = true
            // _this.proTitleList = ['产品优势','服务指南','注意事项'];
            _this.geneSelectListData = res.response.otherInfo && res.response.otherInfo.scheme
          }
          if(_this.goFlag.bohaiLifeFlag){ //判断产品类型确认是否自动试算
             this.tryCalculation()
          }
          // 监听页面滚动
          _this.$nextTick(() => {
            console.log(123123)
            window.addEventListener('scroll', _this.onScrollHanlder)
          })

          // 分享
          let shareData = window.data_source_100001843 && window.data_source_100001843.shareConfig ? window.data_source_100001843.shareConfig : null
          share && share.setShare({
            title: _this.productBase.name || shareData.title,
            desc: _this.productBase.subTitle || shareData.desc,
            imgUrl: shareData.imgUrl,
            link: window.location.href
          })

          switch (_this.productPlans.length) {
            case 2:
              return _this.plansNum = 'two-price'
              break
            case 3:
              return _this.plansNum = 'three-price'
              break
            case 4:
              return _this.plansNum = 'four-price'
              break
          }
          
        } else {
          toast({message: res.message})
        }
      })
    },
    /* 跳转至真实案例 */
    goRealPage (code) {
      this.$router.push({path: 'realExample', query: {code: code, productId: this.itemId}})
    },
    /* 点击附加险投保计划是否展开 */
    toggleFJXPlans (index) {
      this.productAbleAddItemList[index].show = !this.productAbleAddItemList[index].show
    },
    /* 点击就医通道展开关闭 */
    toggleJytdText () {
      this.jytdText = !this.jytdText
    },
    // 点击视频播放
    videoPlay () {
      /* 视频播放 */
      let video = document.getElementById('mainVideo')
      $('#mainVideo').css('display', 'block')
      $('.video-mask').css('display', 'none')
      video.play()
    },
    // 模拟锚点跳转
    goAnchor (selector, idx) {
      let _this = this
      window.removeEventListener('scroll', this.scrollToHtmlTop)
      let anchor = this.$el.querySelector(selector)
      let total = anchor.offsetTop - 50
      // 平滑滚动，时长500ms，每10ms一跳，共50跳
      // 获取当前滚动条与窗体顶部的距离
      let distance = document.documentElement.scrollTop || document.body.scrollTop
      // 计算每一小段的距离
      let step = total / 50
      if (total > distance) {
        smoothDown()
      } else {
        let newTotal = distance - total
        step = newTotal / 50
        smoothUp()
      }

      function smoothDown () {
        if (distance < total) {
          distance += step
          // 移动一小段
          document.body.scrollTop = distance
          document.documentElement.scrollTop = distance
          // 设定每一次跳动的时间间隔为10ms
          setTimeout(smoothDown, 10)
        } else {
          // 限制滚动停止时的距离
          document.body.scrollTop = total
          document.documentElement.scrollTop = total

          _this.activeItem = idx
        }
      }

      function smoothUp () {
        if (distance > total) {
          distance -= step
          document.body.scrollTop = distance
          document.documentElement.scrollTop = distance
          setTimeout(smoothUp, 10)
        } else {
          document.body.scrollTop = total
          document.documentElement.scrollTop = total
          _this.activeItem = idx
        }
      }
    },
    /* 滑动吸顶 */
    scrollToHtmlTop () {
      let _this = this
      /* 锚点定位 */

      let wrapHeight = $('.introduction-title-area').offset().top
      let scrollTop = $('html,body').scrollTop()
      let $getPriceWarp = $('.introduction-title-con')

      if (scrollTop >= wrapHeight) {
        $getPriceWarp.addClass('fixed-start')
      } else {
        $getPriceWarp.removeClass('fixed-start')
      }

      window.addEventListener('scroll', this.onScrollHanlder)
    },
    onScrollHanlder () {
      let _this = this
      let wrapHeight = $('.introduction-title-area').offset().top
      let gmxzHeight = $('#anchor-1').offset().top - 150
      let lplcHeight = $('#anchor-2').offset().top - 100
      let cjwtHeight = $('#anchor-3').offset().top - 250
      let $getPriceWarp = $('.introduction-title-con')
      let winHeight = $(window).height()
      let scrollTop = $(window).scrollTop() || $('document').scrollTop()

      if (scrollTop >= wrapHeight) {
        $getPriceWarp.addClass('fixed-start')
      } else {
        $getPriceWarp.removeClass('fixed-start')
      }

      if (scrollTop >= gmxzHeight) {
        _this.activeItem = 1
      }
      if (scrollTop >= lplcHeight) {
        _this.activeItem = 2
      }
      if (scrollTop >= cjwtHeight) {
        _this.activeItem = 3
      }
      if (scrollTop <= gmxzHeight) {
        _this.activeItem = 0
      }
      // _this.scrollTimer = setTimeout(function () {

      // }, 100);
    },
    /* 保费试算计算器是否展示 */
    showCalculation () {
      this.isShowCalculation = !this.isShowCalculation
      if (this.isShowCalculation) {
        noScroll()
      } else {
        canScroll()
      }
      // noScroll()
    },
    /* 选择交费期限 */
    checkedItemIdx (idx) {
      this.checkedlimitYear = this.limitYear[idx]
      // console.log(this.checkedlimitYear);
    },
    /* 选择出生日期 */
    showPlugin () {
      let _this = this
      let maxAge = timestampToTime(this.productRule.insuredAgeLimit.maxAgeTime)
      let minAge = timestampToTime(this.productRule.insuredAgeLimit.minAgeTime)
      let limitText = '投保年龄区间为' + this.productRule.insuredAgeLimit.minAgeInfo + '-' + this.productRule.insuredAgeLimit.maxAgeInfo
      this.$vux.datetime.show({
        clearText: limitText,
        cancelText: '取消',
        confirmText: '确定',
        format: 'YYYY-MM-DD',
        value: '2017-05-20',
        startDate: minAge,
        endDate: maxAge,
        onConfirm (val) {
          // let current = moment();
          let birth = moment(val)
          _this.age = Math.floor(moment(new Date()).diff(moment(birth), 'years', true))
          // 转成年龄传给试算接口
          // _this.age = current.diff(birth, 'years', true);
          _this.dateVal = val
          _this.tryCalculation()
        },
        onShow () {
        },
        onHide () {
        }
      })
    },
    /**投保性别及年龄 存入金额 月标准 和 年标准*/
    showPremiumLimit() {
      let _this = this;
      let premiumLimit = _this.productRule.premiumLimit;
      let totalBase = [];
      let depositMethod = [];
      for(var i=0;i<premiumLimit.length;i++){
        let res = premiumLimit[i];
        let numAry = _this.getScope( res.singlePremiumMax.value,res.singlePremiumMin.value, res.premiumMultiple.value, res.singlePremiumMin.unitName)
        let numMin = res.singlePremiumMin.value + res.singlePremiumMin.unitName;
        let baseDate = {
          tryCalDate: [ ["男", "女"], _this.getScope(res.ageMax.value, res.ageMin.value, 1, res.ageMin.unitName) ],
          tryCalModel: ["男", res.ageMin.value + res.ageMin.unitName],
          key: res.depositMethod
        };
        if (res.depositMethod == 1) {//每月
          depositMethod.push("每月");
          baseDate['tryCalNumDate'] = [depositMethod, numAry]
          baseDate['tryCalNumModel'] = ['每月', numMin],
          baseDate['name'] = '每月';
        } else if (res.depositMethod == 2) {//每年
          depositMethod.push("每年");
          baseDate['tryCalNumDate'] = [depositMethod, numAry]
          baseDate['tryCalNumModel'] = ['每年', numMin]
          baseDate['name'] = '每年';
        }
        totalBase.push(baseDate)
      }
      _this.productRule.premiumLimit = totalBase;
    },
    /*计算年龄和金额区间ary */
    getScope(max,min,step,unitName){
      let len = (max - min) / step;
      let ary = [];
      for(var i = 0; i<= len; i++){
        ary.push( parseInt(min) + step * i + unitName)
      }
      return ary;
    },
    /**获取保费限制 model值 */
    getpremiumMsg(type){
      let _this = this;
      let base = _this.productRule.premiumLimit;
      if(typeof type == 'number'){ //初始化赋值
          let d = base.filter(res => res.key == type)[0]
          this.premiumLimit = d;
          this.age = parseInt(d.tryCalModel[1]).toString();
          this.priceNum = parseInt(d.tryCalNumModel[1]).toString();
          this.paymentWay = '1';
      }else if( (typeof type != 'number' && type[0] == '每年') ||  (typeof type != 'number' && type[0] == '每月')){ 
          let d = base.filter(res => res.name == type[0])[0]
          this.premiumLimit = d;
          this.premiumLimit.tryCalNumModel = type;
          this.priceNum = parseInt(type[1]).toString();
          this.paymentWay = d['key'].toString();
      }else{ //切换赋值type
          this.premiumLimit.tryCalModel = type;
          this.sex = type[0] == '男' ? '1' : '2'
          this.age = parseInt(type[1]).toString()
      }
      //this.$forceUpdate()
      console.log('premiumLimit', this.premiumLimit)
    },
    /**优化 tyle类型变化时年龄区间不渲染 的问题 待优化*/
    setTryCalModel(v){
      let dom = this.$refs['sexAge'].$el.querySelectorAll('.pickStyle-right')[0];
      dom.innerHTML = v.indexOf('岁') > 0 ? v : this.premiumLimit.tryCalModel[1];
    },
    // 获取试算参数
    getCalcParams () {
      console.log('getCalcParams' , this.sex,this.age ,this.priceNum , this.paymentWay)
      let list = []
      for (let k = 0; k < this.calcProList.length; k++) {
        let listItem = {}
        let productCode = this.calcProList[k]
        if (k === 0) {
          if(productCode == '1100062703'){ //渤海年金
              listItem = {
              'productCode': productCode,
              'buyNumber': this.buyNumber,
              'mainProductCode': '',
              'paymentDate': this.paymentDate, // 交费期间
              'paymentDateUnit': this.payPeriodsUnit, // 缴费方式单位
              'age': typeof this.age !== 'undefined' ? this.age.toString() : '',
              'sex': this.sex.toString(),
              'priceNum':this.priceNum, //存入金额
              'receiveMoneyTime': this.receivAgeDate, //领取年龄
              'receiveMoneyUnit': this.receivAgeUnit, //领取年龄单位
              'receiveWay':this.receiveCycleVal, //领取方式 
              'paymentWay':this.paymentWay.toString() //缴费方式
            }
          }else{
            listItem = {
            'productCode': productCode,
            // "priceNum": this.priceNum,
            'buyNumber': this.buyNumber,
            'mainProductCode': '',
            'insureDate': this.insurancePeriodsDefaultValues, // 保障期间
            'insureDateUnit': this.insureDateUnit, // 保障期间单位
            'paymentDate': this.paymentDate, // 缴费方式
            'paymentDateUnit': this.payPeriodsUnit, // 缴费方式单位
            'insurePlan': this.amountsDefaultValues,
            'socailSecurity': this.socialSecuritiesDefaultValues,
            'age': typeof this.age !== 'undefined' ? this.age.toString() : '',
            'haveSmoke': typeof this.smokedListDefaultValues !== 'undefined' ? this.smokedListDefaultValues.toString() : '',
            'sex': typeof this.genderListDefaultValues !== 'undefined' ? this.genderListDefaultValues.toString() : '',
            'newBill': this.isNewOrder
          }
          }
          
        } else {
          listItem = {
            'productCode': productCode,
            // "priceNum": this.priceNum,
            'buyNumber': this.buyNumber,
            'mainProductCode': this.productCode,
            'insureDate': this.insurancePeriodsDefaultValues, // 保障期间
            'insureDateUnit': this.insureDateUnit, // 保障期间单位
            'paymentDate': this.paymentDate, // 缴费方式
            'paymentDateUnit': this.payPeriodsUnit, // 缴费方式单位
            'insurePlan': this.amountsDefaultValues,
            'socailSecurity': this.socialSecuritiesDefaultValues,
            'age': typeof this.age !== 'undefined' ? this.age.toString() : '',
            'haveSmoke': typeof this.smokedListDefaultValues !== 'undefined' ? this.smokedListDefaultValues.toString() : '',
            'sex': typeof this.genderListDefaultValues !== 'undefined' ? this.genderListDefaultValues.toString() : '',
            'newBill': this.isNewOrder
          }
        }
        list.push(listItem)
      }
      return list
    },
    // 教育楼层 立即查看
    goSeeClassroom (url) {
      if (url) {
        window.location.href = url
      }
    },
    showreceivAgePanel(){
      this.isShowFullPanelReceivAge = true;
    },
    showreceivWayPanel(){
      this.isShowReceivWay = true;
    },
    closeFullPanel() {
      this.isShowFullPanelReceivAge = false;
      this.isShowReceivWay = false;
    }
  },
  computed: {
    /* 计算总金额 */
    totalMoney: function () {
      if (this.initCalcu) {
        return this.sumPrice
      } else {
        let total = 0
        let totalM = 0
        this.beginMoney = this.productBase.price.value && this.productBase.price.value !== 'null' ? this.productBase.price.value : 0
        if (this.goFlag.firstNFlag) {
          this.beginMoney = this.productBase.originalPrice.value
        }
        if (this.activePlansMoney) {
          total += Number(this.activePlansMoney)
        }
        if (this.fjxTotalPrice) {
          total += Number(this.fjxTotalPrice)
        }
        // console.log(total)
        if (total >= Number(this.beginMoney)) {
          return total
        } else {
          return total + Number(this.beginMoney)
        }
      }
    },
    /* 附加险右侧金额 */
    fjxCheckedPlanMoney2: function () {
      if (this.fjxCheckedPlanMoney !== '') {
        return this.fjxCheckedPlanMoney
      }
    }
  },
  // beforeRouteLeave(to, from, next) {
  //     // ....
  //     if (to.name === 'setInsuranceOne') {
  //       if (!to.meta.keepAlive) {
  //           to.meta.keepAlive = true;
  //       }
  //     }
  //     next()
  // },
  beforeRouteEnter (to, from, next) {
    // ....
    if (from.name === 'setInsuranceOne' || from.name === 'setInsuranceGene') {
      if (from.meta.keepAlive) {
        from.meta.keepAlive = false
      }
    }
    next()
  },
  watch: {
    productBase (d) {
      if (d.name) {
        document.title = d.name
      }
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" type="text/scss">
  @import "../../assets/scss/common/_rem.scss";
  @import "../../assets/scss/common/_base.scss";
  @import "../../assets/scss/common/_animation.scss";
  .product-details-page {
    background-color: #f5f5f5;

    .bottom-btn-area {
      .bottom-btn-con {
        z-index: 1002;
      }
    }

    .reservation-img {
      padding-right: px2rem(32);
      // background: #ccc;
      box-sizing: border-box;
      margin-bottom: px2rem(32);
      img {
        display: block;
        width: 100%;
      }
    }
  }
  .product-details-common {
    //padding-bottom: px2rem(120);
    .banner-video-area {
      width: 100%;
      min-height: px2rem(290);
      max-height: px2rem(390);
      overflow: hidden;
      position: relative;
      #mainVideo {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 9;
        width: 100%;
        height: px2rem(290);
        display: none;
        object-fit: fill;
      }
      .video-mask {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 10;
        width: 100%;
        height: px2rem(290);
        img {
          width: 100%;
          height: px2rem(290);
        }
        .play-btn {
          position: absolute;
          top: 50%;
          left: 50%;
          background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/10891/7/5694/1581/5c18bc84Ea7bb52cf/1506889d82a6c28c.png");
          background-size: cover;
          width: px2rem(80);
          height: px2rem(80);
          margin-top: px2rem(-40);
          margin-left: px2rem(-40);
        }
      }
    }
    #mainVideo::-webkit-media-controls-fullscreen-button {
      display: none;
    }
    #mainVideo::media-controls-fullscreen-button {
      display: none;
    }

    .dp-header .dp-item {
      height: px2rem(120) !important;
      line-height: px2rem(120) !important;
      word-wrap: keep-all;
    }
    .dp-header .dp-item.dp-left {
      color: $mainTextColor !important;
      font-size: px2rem(28) !important;
    }
    .dp-header .dp-item.dp-right {
      color: $mainTextColor !important;
      font-size: px2rem(28) !important;
    }
    .vux-datetime-clear {
      width: px2rem(320) !important;
      flex: auto !important;
      color: #999999 !important;
      font-size: px2rem(28) !important;
    }
    .insurace-try-calculation {
      background-color: #fff;
      height: px2rem(750);
      position: fixed;
      bottom: px2rem(100);
      left: 0;
      z-index: 1001;
      border-top-left-radius: px2rem(20);
      border-top-right-radius: px2rem(20);
      overflow: hidden;
      width: 100%;
      padding-bottom: constant(safe-area-inset-bottom) !important;
      padding-bottom: env(safe-area-inset-bottom) !important;
      .try-calculation-box {
        height: 100%;
        position: relative;
        .title {
          height: px2rem(120);
          line-height: px2rem(120);
          padding-left: px2rem(32);
          position: relative;
          border-bottom: 1px solid #e6e6e6;
          span {
            font-size: px2rem(36);
            color: #333333;
            font-weight: bold;
          }
          .sub-title {
            font-size: px2rem(24);
            color: #999999;
          }
          .close-icon {
            width: px2rem(40);
            height: px2rem(40);
            position: absolute;
            top: 50%;
            right: px2rem(32);
            margin-top: px2rem(-20);
            background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/31844/2/4586/516/5c7e24b3E244a401d/c4ab39fc1ea1938e.png");
            background-size: cover;
          }
        }
        .try-calculation-win {
          width: 100%;
          position: absolute;
          top: px2rem(120);
          bottom: 0;
          left: 0;
          right: 0;

          .try-calculation-ul {
            //max-height: px2rem(621);
            //overflow-y: auto;
            padding: 0 px2rem(32);
            .try-calculation-list {
              height: px2rem(112);
              line-height: px2rem(112);
              border-bottom: 1px solid #e6e6e6;
              display: flex;
              align-items: center;
              .key {
                font-size: px2rem(32);
                color: #333333;
                max-width: px2rem(500);
                margin-right: px2rem(20);
                //float: left;
                p {
                  width: 100%;
                  overflow:hidden;
                  white-space:nowrap;
                  text-overflow:ellipsis
                }
              }
              .val {
                flex: 1;
                min-width: 0;
                vertical-align: middle;
                display: flex;
                align-items: center;
                height: px2rem(112);
                //float: right;
                //width: px2rem(480);
                .choose-item-box {
                  width: 100%;
                  height: px2rem(112);
                  display: flex;
                  align-items: center;
                  justify-content: flex-end;
                  .item {
                    text-align: center;
                    font-size: px2rem(28);
                    color: #444444;
                    background: #F5F5F5;
                    border-radius: px2rem(28);
                    padding: 0 px2rem(24);
                    height: px2rem(56);
                    line-height: px2rem(56);
                    margin-left: px2rem(10);
                  }
                }
                .go-choose-box {
                  width: 100%;
                  height: px2rem(112);
                  display: flex;
                  align-items: center;
                  justify-content: flex-end;
                  span {
                    font-size: px2rem(32);
                    color: #CCCCCC;
                  }
                  .colFour {
                    color: #444 !important;
                  }
                  .sj-icon {
                    width: px2rem(14);
                    height: px2rem(24);
                    background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/32171/4/4484/230/5c7e4a8eE254f6f6a/7b76e30e4ee79548.png");
                    background-size: cover;
                    display: block;
                    margin-left: px2rem(15);
                  }
                }
              }
            }
            .try-calculation-list:last-of-type {
              border-bottom: none;
            }
          }
        }

      }
    }

    .bottom-arguments-area {
      width: px2rem(694);
      padding-top: px2rem(32);
      padding-bottom: px2rem(50);
      margin: 0 auto;
      .bottom-arguments-box {
        position: relative;
        padding-left: px2rem(50);
        font-size: px2rem(24);
        color: #999999;
        a {
          color: #5b9fe2;
        }
        .arguments-check-box {
          position: absolute;
          top: 0;
          left: 0;
          border-radius: px2rem(21);
          width: px2rem(30);
          height: px2rem(30);
          border: 1px solid #ccc;
        }
        .arguments-checked {
          width: px2rem(30);
          height: px2rem(30);
          border: none;
          @include i-checked;
        }
      }
    }
    .mt20Rem {
      margin-top: px2rem(20) !important;
    }
    .product-details-page {
      padding-bottom: px2rem(120);
    }
    .product-details-con {
      background-color: #fff;

    }
    .details-banner-area {
      width: 100%;
      min-height: px2rem(290);
      max-height: px2rem(390);
      position: relative;
      .company-name-box {
        position: absolute;
        top: px2rem(30);
        left: px2rem(30);
        font-size: px2rem(24);
        color: #FFFFFF;
        text-shadow: 0 0 px2rem(10) rgba(0, 0, 0, 0.50);
        height: px2rem(40);
        line-height: px2rem(40);
        .logo {
          width: px2rem(40);
          height: px2rem(40);
          display: inline-block;
          vertical-align: middle;
          position: relative;
          top: px2rem(-4);
          /*margin-right: px2rem(4);*/
          background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/18727/36/8630/2133/5c778fc9E5f3fe328/59b50c53cbe60bf7.png");
          background-size: cover;
        }
      }
      .img-box {
        width: 100%;
        /*height: px2rem(290);*/
        img {
          width: 100%;
          /*height: px2rem(290);*/
        }
      }
    }
    .product-choose-infor + .withicon-insurance-link {
       position: relative;
       &:before {
         content: "";
         border-top: 1px solid #e6e6e6;
         position: absolute;
         left: px2rem(28);
         top: 0;
         right: 0;
       }
    }
    .product-choose-infor {
      padding-top: px2rem(28);
      padding-left: px2rem(28);
      overflow: hidden;
      .product-choose-box {
        padding-right: px2rem(28);
        //border-bottom: 1px solid #e6e6e6;
        padding-bottom: px2rem(30);
      }
      .pro-name {
        font-size: px2rem(44);
        color: #333333;
        line-height: px2rem(62);
        font-weight: bold;
      }
      .pro-sub-describe {
        margin-top: px2rem(12);
        font-size: px2rem(28);
        color: #333333;
        line-height: 1;
      }
      .pro-tags-area {
        margin-top: px2rem(24);
        font-size: px2rem(24);
        color: #999999;
        height: px2rem(50);
        line-height: px2rem(50);
        position: relative;
        .money-calculation-btn {
          position: absolute;
          top: 0;
          right: 0;
          color: $mainTextColor;
          font-size: 0;
          .calculation-icon {
            width: px2rem(50);
            height: px2rem(50);
            display: inline-block;
            vertical-align: middle;
            @include i-calculator;
            position: relative;
            top: -1px;
          }
          span {
            height: px2rem(50);
            display: inline-block;
            vertical-align: middle;
            font-size: px2rem(24);
          }
        }
        .tags-box {
          span {
            display: inline-block;
            vertical-align: middle;
          }
          .line {
            width: 1px;
            display: inline-block;
            vertical-align: middle;
            height: px2rem(18);
            margin: 0 px2rem(10);
            background-color: #d8d8d8;
          }
        }

      }
      .price-choose-area {
        margin-top: px2rem(30);
        .price-choose-box {
          display: flex;
          justify-content: space-between;
          .price-choose-item {
            height: px2rem(150);
            background: #F5F5F5;
            border-radius: 6px;
            text-align: center;
            .up-price {
              font-size: px2rem(28);
              color: #333333;
              padding-top: px2rem(20);
              i {
                font-weight: bold;
              }
              em {
                font-family: 'UDC-Bold';
                font-size: px2rem(44);
                color: #333333;
              }
            }
            .insurance-limit {
              font-size: px2rem(24);
              color: #999999;
              line-height: 1.2;
              max-height: px2rem(56);
              word-break: break-all;
              text-overflow: ellipsis;
              display: -webkit-box;
              /* autoprefixer: off */
              -webkit-box-orient: vertical;
              /* autoprefixer: on */
              -webkit-line-clamp: 2;
              overflow: hidden;
            }
          }
          .price-checked {
            @include gradientbg;
            .up-price {
              color: #fff;
              em {
                color: #fff;
              }

            }
            .insurance-limit {
              font-size: px2rem(24);
              color: #fff;
            }
          }
        }
        .two-price {
          .price-choose-item {
            width: px2rem(334) !important;
          }
        }
        .three-price {
          .price-choose-item {
            width: px2rem(210) !important;
          }
        }
        .four-price {
          .price-choose-item {
            width: px2rem(158) !important;
          }
        }
      }

      .insurance-infor-lists {
        padding-top: px2rem(30);
        ul {
          li {
            margin-bottom: px2rem(10);
            color: #333;
            .key {
              float: left;
              font-size: px2rem(28);
            }
            .val {
              float: right;
              text-align: right;
              font-size: px2rem(28);
              color: #999;
            }
          }
        }
      }
      .check-insurance-details {
        font-size: px2rem(28);
        color: $mainTextColor;
        display: block;
        margin-top: px2rem(20);
      }
      .insurance-classroom-area{
        margin-right: px2rem(28);
        margin-bottom: px2rem(28);
        .classroom{
          color: #4C4C4C;
          background: #FFFAF1;
          border-radius: px2rem(10);
          padding: px2rem(30) px2rem(38);
          letter-spacing: 0;
          box-sizing: border-box;
          .common-font{
            font-size: px2rem(28);
            text-align: justify;
          }
          .classroom-title{
            display: flex;
            justify-content: flex-start;
            align-items: center;
            .title-icon{
              width: px2rem(46);
              height: px2rem(35);
              margin-right: px2rem(24);
            }
            .title-text{
              flex: 1;
              font-size: px2rem(30);
              line-height: px2rem(30);
              font-weight: 600;
            }
          }
          .classroom-info{
            line-height: px2rem(40);
            margin-top: px2rem(13);
          }
          .classroom-more{
            color: #53AAB2;
            text-align: right;
            margin-top: px2rem(20);
          }
        }
      }
      .insurance-additional-area {
        margin-top: px2rem(20);
        padding-bottom: px2rem(20);
        border-bottom: 1px solid #e6e6e6;
        &:last-child {
          border-bottom: none;
        }
        .title {
          height: px2rem(100);
          line-height: px2rem(100);
          font-size: px2rem(32);
          font-weight: bold;
          color: #333333;
        }
        ul {
          padding-right: px2rem(28);
          .insurance-additional-list {
            margin-bottom: px2rem(20);
            // position: relative;
            // padding-left: px2rem(68);
            /*height: px2rem(130);*/
            /*border-bottom: 1px solid #e6e6e6;*/

            .insurance-additional-top {
              display: flex;
              align-items: center;
              justify-content: space-between;
              .infor-additional-box {
                //width: px2rem(480);
                flex: 1;
                margin: 0 px2rem(40);
              }
            }

            .insurance-additional-bottom {
              display: flex;
              padding-left: px2rem(68);
              /*padding-bottom: px2rem(24);*/
              span {
                display: block;
                margin-right: px2rem(15);
                height: px2rem(56);
                line-height: px2rem(56);
                padding: 0 px2rem(23);
                background: #F5F5F5;
                border-radius: px2rem(28);
                display: flex;
                align-items: center;
                justify-content: center;
                // margin-top: px2rem(10);
              }
              .fjxCheckedPlan {
                font-weight: bold;
                @include fjx-tag-checked
              }
            }
            .check-box {
              // position: absolute;
              // top: px2rem(38);
              // left: 0;
              width: 20px;
              height: 20px;
              .check-btn {
                display: block;
                border-radius: 50%;
                overflow: hidden;
                width: 100%;
                height: 100%;
                border: 1px solid #ccc;
              }
              .check-btn:checked {
                // width: px2rem(40);
                // height: px2rem(40);
                border: none;
                @include i-checked;
              }
            }

            .infor-additional-box {
              h3 {
                font-size: px2rem(28);
                color: #333333;
                font-weight: bold;
                // padding-top: px2rem(24);
                line-height: px2rem(40);
              }
              p {
                font-size: px2rem(24);
                color: #999999;
              }
            }

            .up-price-additional {
              height: px2rem(130);
              line-height: px2rem(130);
              font-size: px2rem(28);
              color: #F15A5B;
              i {
                font-weight: bold;
              }
              em {
                font-family: 'UDC-Bold';
                font-size: px2rem(44);
                color: #F15A5B;
              }
            }
          }
        }
      }
    }
    .withicon-insurance-link {
      min-height: px2rem(100);
      line-height: px2rem(100);
      padding-left: px2rem(92);
      position: relative;
      background-color: #fff;
      .withicon-insurance-con {
        line-height: px2rem(48);
        color: #666;
        padding-right: px2rem(32);
        font-size: px2rem(28);
        padding-bottom: px2rem(20);
      }
      .img-box {
        position: absolute;
        top: px2rem(25);
        left: px2rem(32);
        height: px2rem(48);
        width: px2rem(48);
      }
      .xts-icon {
        @include i-xts;
      }
      .i-zsfw {
        @include i-zsfw;
      }
      .lstd-icon {
        @include i-lstd;
      }
      .lpal-icon {
        @include i-lpal;
      }
      p {
        font-size: px2rem(28);
        color: #444444;
      }
      .link-sj {
        width: px2rem(14);
        height: px2rem(24);
        background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/15627/4/8625/230/5c77ea63Ea10904c5/bbe82c7c31a04f00.png");
        background-size: cover;
        display: block;
        position: absolute;
        top: px2rem(36);
        right: px2rem(32);
        //margin-top: px2rem(-12);
      }
      .down-sj {
        width: px2rem(14);
        height: px2rem(24);
        background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/15627/4/8625/230/5c77ea63Ea10904c5/bbe82c7c31a04f00.png");
        background-size: cover;
        display: block;
        position: absolute;
        top: px2rem(36);
        right: px2rem(32);
        transform: rotate(90deg);
      }
      .up-sj {
        transform: rotate(-90deg);
      }
    }
    /*产品介绍*/
    .product-introduction-area {
      margin-top: px2rem(20);
    }
    .introduction-title-area {
      height: px2rem(120);
      line-height: px2rem(120);
      width: 100%;
      background-color: #fff;
      .introduction-title-box {
        width: px2rem(700);
        margin: 0 auto;
      }
      .introduction-title-con {
        width: 100%;
        margin: 0 auto;
        font-weight: bold;
        font-size: px2rem(32);
        color: #999999;
        background-color: #fff;
        .title-item {
          float: left;
          width: 25%;
          text-align: center;
          opacity: 0.8;
          position: relative;
          .bottom-line {
            width: px2rem(40);
            height: px2rem(6);
            background-color: #333;
            position: absolute;
            bottom: px2rem(21);
            left: 50%;
            margin-left: px2rem(-20);
            display: none;
          }
        }
        .active-item {
          font-weight: bold;
          font-size: px2rem(40);
          color: #333333;
          opacity: 1;
          .bottom-line {
            display: block;
          }
        }
      }
      .fixed-start {
        position: fixed;
        top: 0;
        left: 0;

        z-index: 100;
      }
    }
    .product-introduction-box {
      img {
        display: block;
        width: 100%;
      }
    }
    .introduction-img-box {
      width: 100%;
      overflow: hidden;
      img {
        width: 100%;
      }
    }
    .introduction-infor-con {
      padding-left: px2rem(28);
      background-color: #fff;

      .title {
        font-size: px2rem(32);
        color: #333333;
        font-weight: bold;
        margin-bottom: px2rem(30);
        position: relative;
        .claims-now {
          position: absolute;
          top: 0;
          right: px2rem(0);
          font-size: px2rem(28);
          color: $mainTextColor;
        }
      }
      .introduction-infor-box {
        padding-right: px2rem(28);
        border-bottom: 1px solid #eee;
        padding-top: px2rem(40);
        padding-bottom: px2rem(40);
        .p-list {
          font-size: px2rem(28);
          color: #666666;

          line-height: px2rem(48);
        }
      }
      .introduction-infor-box:last-of-type {
        border-bottom: none;
      }
      .more-question {
        font-size: px2rem(24);
        color: #999999;
        margin-top: px2rem(30);
        // text-align: center;
        a {
          color: #333;
        }
      }
      .introduction-infor-main {
        .process-list-box {
          /*border-bottom: 1px solid #eee;*/
          position: relative;
          /*padding: .1rem .15rem .2rem px2rem(65);*/
          .process-list-con {
            position: relative;
            margin-top: px2rem(30);
            padding-left: px2rem(68);
            color: #666;
            /*padding-right: px2rem(100);*/
            h2 {
              font-size: .15rem;
              color: #444;
              font-weight: 700;
              margin-bottom: px2rem(10);
            }
            .process-content {
              min-height: px2rem(96);
              font-size: .14rem;
              color: #666;
              letter-spacing: 0;
              text-align: justify;
              line-height: .24rem;
            }
            .process-circle {
              position: absolute;
              top: -.025rem;
              left: 0;
              /*background: #7AB1B8;*/
              width: .24rem;
              height: .24rem;
              text-align: center;
              line-height: .24rem;
              color: #fff;
              border-radius: 50%;
              z-index: 10;
              @include gradientbg;
            }
            .line {
              width: 2px;
              height: 100%;
              position: absolute;
              top: .215rem;
              left: .11rem;
              background: $mainTextColor;
            }
          }
          .question-list-con {
            padding-bottom: px2rem(30);
            // border-bottom: 1px solid #eee;
          }
        }

      }
    }
  }

  .dp-header .dp-item {
    word-break: keep-all;
    font-size: px2rem(30);
  }
  .dp-header .dp-item.dp-right,.dp-header .dp-item{
    color: $mainTextColor;
  }
  .jmv-toast.default {
    z-index: 1020!important;
  }

  .product-bohaiLife-box{
    padding-right: px2rem(32);
    overflow: hidden;
    .try-calculation-ul{
      .try-calculation-list {
              height: px2rem(112);
              line-height: px2rem(112);
              border-bottom: 1px solid #e6e6e6;
              display: flex;
              align-items: center;
              justify-content:space-between;
              box-sizing: border-box;
              .key {
                font-size: px2rem(32);
                font-family: 'PingFangSC-Medium';
                color: #333333;
                max-width: px2rem(300);
              }
              .val {
                width:px2rem(288);
                height: px2rem(72);
                border: solid 1px #53AAB2;
                border-radius: px2rem(4);
                min-width: 0;
                vertical-align: middle;
                display: flex;
                align-items: center;
                justify-content: space-between;
                position: relative;
                background: #fff;
                .picker-box{
                  position: absolute;
                  opacity: 0;
                  width: 100%;
                  left: 0;
                  top:0;
                }
                /*去掉vux自带样式*/
                .vux-no-group-title{
                  margin-top: 0;
                }
                .weui-cells{
                  margin-top: 0;
                  background-color: transparent;
                  line-height: px2rem(72);
                  font-size: px2rem(28);
                  .weui-cell{
                    padding: 0;
                    .vux-popup-picker-value{
                      display: block;
                    }
                  }
                }
                /*去掉vux自带样式*/

                .val-detail-key{
                  width: px2rem(136);
                  height: px2rem(72);
                  background:rgba(83,170,178,.1);
                  font-size: px2rem(28);
                  color: #53AAB2;
                  font-family: 'PingFangSC-Medium';
                  text-align: center;
                  line-height: px2rem(72);
                  position: relative;
                  &:after{
                    content: "";
                    position: absolute;
                    right: 0;
                    bottom: 0;
                    border-right: 0;
                    border-top: 0;
                    border-bottom: px2rem(72) rgba(255,255,255,1) solid;
                    border-left: px2rem(36)  rgba(83,170,178,0) solid;
                  }
                }
                .val-detail-value{
                  font-family: 'PingFangSC-Regular';
                  font-size: px2rem(28);
                  color: #666666;
                  display: flex;
                  justify-content: center;
                  align-items: center;
                  padding-right: px2rem(10);
                  .sj-icon {
                    width: px2rem(14);
                    height: px2rem(24);
                    background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/32171/4/4484/230/5c7e4a8eE254f6f6a/7b76e30e4ee79548.png");
                    background-size: cover;
                    display: block;
                    margin-left: px2rem(15);
                  }
                }
              }
            }
      
    
    }
    .try-calculation-total{
      height: px2rem(144);
      display: flex;
      align-items: center;
      justify-content:space-between;
          .total-key{
             display: flex;
             flex-direction: column;
             text-align: left;
             .key{
               font-family: 'PingFangSC-Medium';
               font-size: px2rem(32);
               color: #333333;
               display: block;
               height: px2rem(45);
               line-height: px2rem(45);
             }
            .standard{
              display: block;
              font-family: PingFangSC-Regular;
              font-size: px2rem(24);
              color: #999999;
              height: px2rem(33);
              line-height: px2rem(33);
            }
          }
          .total-val{
            color: #EF4034;
            .figure{
              font-family: 'UDC1.04-Bold';
              font-size: px2rem(48);
              color: #EF4034;
            }
            .unit{
              font-family: 'PingFangSC-Regular';
              font-size: px2rem(32);
              color: #EF4034;
            }
          }
      }
      .try-calculation-getway{
        background: #FFFFFF;
        box-shadow: 0 px2rem(4) px2rem(20) 0 rgba(0,0,0,0.07);
        position: relative;
        left:2%;
        border-radius: px2rem(8);
        height: px2rem(293);
        padding:0 px2rem(28);
        margin-bottom: px2rem(23);
        .try-calculation-list{
          height: px2rem(146);
          border-bottom: solid 1px #EEEEEE;
          display: flex;
          justify-content: center;
          flex-direction:column;
          box-sizing: border-box;
          &:last-child{
            border-bottom:none;
          }
          .list-title{
            font-family: 'PingFangSC-Regular';
            font-size: px2rem(28);
            color: #333333;
            height: px2rem(40);
            line-height: px2rem(40);
            margin-bottom:px2rem(5);
          }
          .list-val{
            .figure{
              font-family: UDC1.04-Bold;
              font-size: px2rem(40);
              color: #EF4034;
              height: px2rem(48);
              line-height: px2rem(48);
            }
            .unit{
              font-family: 'PingFangSC-Regular';
              font-size: px2rem(32);
              color: #EF4034;
            }
          }
        }
      }
      .pro-select-infor-box{
        .set-insurance-ul{
          .set-insurance-list{
            height: px2rem(112);
            line-height: px2rem(112);
            font-size: px2rem(32);
            color: #333333;
            border-bottom: 1px solid #eeeeee;
            box-sizing: border-box;
            display: flex;
            align-items: center;
            &:last-child{
              border-bottom: none;
            }
            .key {
              // float: left;
              max-width: px2rem(160);
              white-space: nowrap;
              position: relative;
              z-index: 100;
              margin-right: px2rem(20);
              .tips-icon {
                width: px2rem(32);
                height: px2rem(32);
                background-image: url("//storage.jd.com/public-static-resource/insurance/images/icon-explain.png");
                background-size: 100% 100%;
                display: inline-block;
                vertical-align: middle;
                margin-left: px2rem(10);
              }
            }
            .val {
              //float: right;
              flex: 1;
              height: px2rem(112);
              min-width: 0;
              display: flex;
              align-items: center;
              justify-content: flex-end;
              .input-item-box {
                width: px2rem(500);
                text-align: right;
                height: px2rem(112);
                display: flex;
                align-items: center;
                justify-content: flex-end;
                position: relative;

                .gray-text {
                  /*font-weight: 400 !important;*/
                }
                .i-input {
                  display: block;
                  text-align: right;
                  width: 100%;
                  height: px2rem(112);
                  background: none;
                  position: relative;
                  z-index: 2;
                  padding-right: px2rem(36);
                }
                input {
                  display: block;
                  text-align: right;
                  width: 100%;
                  height: px2rem(112);
                  background: none;
                  z-index: 2;
                }
                .cell {
                  position: absolute;
                  right: 0;
                }
                .select-item-box {
                  position: relative;
                  padding-right: px2rem(24);
                  max-width: px2rem(450);
                  min-height: 100%;
                  white-space: nowrap;
                  text-overflow: ellipsis;
                  overflow: hidden;
                  span {
                    white-space: nowrap;
                    text-overflow: ellipsis;
                    overflow: hidden;
                    font-size: px2rem(32);
                    color: #999999;
                  }
                  .right-sj {
                    width: px2rem(14);
                    height: px2rem(24);
                    background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/19229/5/9625/230/5c80cb4eEa4378e05/44d63137bcb3edee.png");
                    background-size: cover;
                    display: block;
                    position: absolute;
                    top: 50%;
                    right: 0;
                    margin-top: px2rem(-12);
                  }
                }
              }
              .upload-pic-area {
                display: flex;
                width: 100%;
                /*width: px2rem(410);*/
                /*width: px2rem(410);*/
                justify-content: flex-end;
                /*justify-content: space-between;*/
                align-items: center;
                .upload-img-box {
                  width: px2rem(120);
                  height: px2rem(120);
                  margin-left: px2rem(20);
                  position: relative;
                  background-color: #f5f5f5;
                  .befor-upload-area {
                    position: relative;
                    width: px2rem(120);
                    height: px2rem(120);
                  }
                  .after-upload-area {
                    position: absolute;
                    z-index: 10;
                    top: 0;
                    left: 0;
                    width: 100%;
                    height: 100%;
                    img {
                      width: px2rem(120);
                      height: px2rem(120);
                    }
                  }
                  input {
                    width: px2rem(120);
                    height: px2rem(120);
                    opacity: 0;
                    position: absolute;
                    top: 0;
                    left: 0;
                    z-index: 11;
                  }
                  .upload-img-front {
                    padding-top: px2rem(20);
                    .icon-add {
                      width: px2rem(44);
                      height: px2rem(44);
                      background-image: url("//img30.360buyimg.com/jr_image/jfs/t1/20265/15/10724/795/5c88b504Eeb77802e/9eb8c044006fac44.png");
                      background-size: cover;
                      margin: 0 auto;
                    }
                    p {
                      text-align: center;
                      margin-top: px2rem(5);
                      font-size: px2rem(24);
                      color: #999999;
                    }
                  }
                }
              }
            }
          }
        }
      }
  }

</style>
