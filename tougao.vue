<el-table-column fixed label="文章" min-width="600px" width="auto" align="left">
  <template slot-scope="scope">
    <div class="table_contentBox_main">
      <div class="imgBox">
        <img :src="scope.row.coverList">
      </div>
      <div class="titleBox">
        <span class="title link-type" @click="handleLink(scope.row.link)">
          {{ scope.row.title }}
          <i v-if="scope.row.tipOff== 1" style="margin-left: 1%;color:#f56c6c;" class="fa fa-exclamation-triangle" aria-hidden="true"></i>
            <svg-icon style="margin-left: 1%;"
              :icon-class="scope.row.type == 1 ? 'graphic': '' ||
              scope.row.type == 2 ? 'video': '' ||
              scope.row.type == 3 ? 'shortVideo': '' ||
              scope.row.type == 4 ? 'microHeadStrip': ''" class-name="icon" />
        </span>
        <!-- scope.row.suggest_status == 1 -->
        <span v-if="scope.row.suggest_status == 1 ||
          scope.row.platStatus == 1 || (scope.row.platStatus == '21' || scope.row.platStatus == '22' || scope.row.platStatus == '23')" :class="scope.row.mcnType == 1 ? 'mcnName_jiaV mcnName':'mcnName'">{{ scope.row.mcnName }}</span>
        <span v-else-if="scope.row.status == 3" class="mcnName">{{ scope.row.cnOrgName }}</span>
        <span v-else class="mcnName">{{ scope.row.suggestOrgName }}</span>
      </div>
      <span class="pushStatusBox" v-if="formQuery.status == '0' || formQuery.status == '1'">
        <label class="label">{{$t('table.pushStatus') + '：'}}</label>
        <div v-if="scope.row.platStatus != '1'">
          <span>只有成功发布的内容才会显示推送状态.</span>
        </div>
        <div v-if="scope.row.platStatus == '1'" class="icons-container">
          <div class="icons-wrapper">
            <div 
              v-for="tag of scope.row.platPushFormList"
              :key="tag.id"
              @click="handleClipboard(generateIconCode(tag),$event)"
            >
              <el-tooltip placement="top">
                <div slot="content">{{ platName(tag) }}</div>
                <div v-if="tag.pushResultStatus==0" slot="content">{{ pushResultStatusCode0(tag) }}</div>
                <div v-if="tag.pushResultStatus==1" slot="content">{{ pushResultStatusCode1(tag) }}</div>
                <div v-if="tag.pushResultStatus==2" slot="content">{{ pushResultStatusCode2(tag) }}</div>
                <div v-if="tag.pushResultStatus==3 && tag.reason !=''" slot="content">{{ pushResultStatusCode3_1(tag) }}</div>
                <div v-if="tag.pushResultStatus==3 && tag.reason ==''" slot="content">{{ pushResultStatusCode3(tag) }}</div>
                <div v-if="tag.pushResultStatus==4" slot="content">{{ pushResultStatusCode4(tag) }}</div>
                <div v-if="tag.pushResultStatus==5" slot="content">{{ pushResultStatusCode5(tag) }}</div>
                <div class="icon-item img_selectBoxFather">
                  <div class="img_selectBox">
                    <span class="el-dropdown-link">
                      <img :src="tag.icon">
                      <i v-if="tag.pushResultStatus==0" class="fa fa-close"></i>
                      <i v-if="tag.pushResultStatus==1" class="fa icon_logoSuccess fa-check check-sun"></i>
                      <i v-if="tag.pushResultStatus==2 || tag.pushResultStatus==4" class="fa icon_logoDanger fa-warning"></i>
                      <i v-if="tag.pushResultStatus==3" class="fa fa-refresh loader-ring-light">
                        <span></span>
                        <span></span>
                        <span></span>
                      </i>
                      <i v-if="tag.pushResultStatus==5" class="fa fa-trash"></i>
                    </span>
                  </div>
                </div>
              </el-tooltip>
            </div>
          </div>
        </div>
      </span>
    </div>
  </template>
</el-table-column>
<el-table-column v-if="tableQuery.position == 3" label="入库时间" width="160px" align="center">
  <template slot-scope="scope">
    <span class="reviewDate" v-if="scope.row.entryStatus == '0'">未入库<br></span>
    <span class="reviewDate" v-if="scope.row.entryStatus == '1'">已入库<br></span>
    <span class="reviewDate">{{scope.row.entrytime}}</span>
  </template>
</el-table-column>
<el-table-column v-if="tableQuery.position == 3 && formQuery.status == '1'" label="选稿电视台" width="200px" align="center">
  <template slot-scope="scope">
    <span>{{ scope.row.mcnName }}</span>
  </template>
</el-table-column>
<el-table-column v-if="tableQuery.position == 1 || tableQuery.position == 2" label="创作人" width="200px" align="center">
  <template slot-scope="scope">
    <span>{{ scope.row.author }}</span>
  </template>
</el-table-column>
<el-table-column label="编辑时间" width="160px" align="center">
  <template slot-scope="scope">
    <span class="reviewDate">{{tableQuery.position == 2 ? scope.row.usedTime : scope.row.createDate}}</span></template>
</el-table-column>      
<el-table-column v-if="tableQuery.position == 1 || tableQuery.position == 2" :label="$t('table.status')" class-name="status-col" width="180">
  <template slot-scope="scope">
    <span class="reviewUser" v-if="scope.row.platStatus == '4'">
      {{ scope.row.operator }}
      <br>
    </span>
    <span class="table_reviewStatus twoStatus" v-if="scope.row.platStatus == '1'">平台已发布
      <br>
    </span>
    <span class="table_reviewStatus oneStatus" v-if="scope.row.platStatus == '21'">平台待审核
      <br>
    </span>
    <span class="table_reviewStatus oneStatus" v-if="scope.row.platStatus == '22'">平台待审核
      <br>
    </span>
    <span class="table_reviewStatus oneStatus" v-if="scope.row.platStatus == '23'">平台待审核
      <br>
    </span>
    <span class="table_reviewStatus threeStatus" v-if="scope.row.platStatus == '3'">平台已驳回
      <br>
    </span>
    <span class="table_reviewStatus threeStatus" v-if="scope.row.platStatus == '4'">平台已删除
      <br>
      <span class="reviewDate" :title="'删除时间'" style="font-size: 12px;">{{scope.row.updateTime}}</span>
      <br>
    </span>
    <span v-if="scope.row.status != '0'">{{ scope.row.publishDate }}<br></span>
    <span v-if="scope.row.isSchedule == 1" style="color:#6A9955">
      <span class="dingshiTitle">定</span>{{scope.row.scheduleTime}}<br></span>
    <span v-if="scope.row.isPush == '0'">未发送
      <br>
    </span>
    <span v-if="scope.row.isPush == '1'">已发送
      <br>
    </span>
    <span v-if="scope.row.isPush == '2'">已取消定时
      <br>
    </span>
  </template>
</el-table-column>
<el-table-column v-if="tableQuery.position == 3" :label="$t('table.status')" class-name="status-col" width="180">
  <template slot-scope="scope">
    <span :title="'操作人'" class="reviewUser" v-if="scope.row.platStatus == '4'">
      {{ scope.row.operator }}
      <br>
    </span>
    <span :class="[scope.row.suggest_status != 1 ? 'oneStatus' : 'threeStatus',
      'table_reviewStatus']" v-if="scope.row.tipOff != 1 && scope.row.delete == '0' && scope.row.status == '0'">
        {{scope.row.suggest_status != 1 ? "电视台待审核" : "电视台未通过"}}
      <br>
    </span>
    <span class="threeStatus table_reviewStatus" 
      v-if="scope.row.tipOff == 1 && scope.row.delete == '0' && scope.row.status == '0'">
        {{scope.row.tipOff == 1 ? "举报" : ""}}
      <br>
    </span>
    <span class="table_reviewStatus threeStatus" v-if="scope.row.delete == '0' && scope.row.status == '3'">电视台未通过
      <br>
    </span>
    <span class="table_reviewStatus threeStatus" v-if="scope.row.delete == '0' && scope.row.platStatus == '3'">平台未通过
      <br>
    </span>
    <span class="table_reviewStatus threeStatus" v-if="(scope.row.delete == '0' || scope.row.delete == '1') && scope.row.platStatus == '4'">
      {{scope.row.delete != '1' ? '平台删除': '已删除'}}
      <br>
      <span class="reviewDate" :title="'删除时间'" style="font-size: 12px;">{{scope.row.updateTime}}</span>
      <br>
    </span>
    <span class="table_reviewStatus threeStatus" v-if="scope.row.delete == '1' && scope.row.platStatus != '4'">
      已删除
      <br>
      <span class="reviewDate" :title="'删除时间'" style="font-size: 12px;">{{scope.row.updateTime}}</span>
      <br>
    </span>
    <span class="table_reviewStatus oneStatus" v-if="(scope.row.delete == '0' || scope.row.delete == '2') && 
    (scope.row.status == '1' || scope.row.status == '2') && 
    (scope.row.platStatus == '21' || scope.row.platStatus == '22' || scope.row.platStatus == '23')">平台待审核
      <br>
    </span>
    <span class="table_reviewStatus twoStatus" v-if="scope.row.delete == '0' && 
    (scope.row.status == '1' || scope.row.status == '2') && scope.row.platStatus == '1'">已发布
      <br>
    </span>
  </template>
</el-table-column>
<el-table-column v-if="(formQuery.status == '3')" label="驳回理由" width="160px" align="center">
  <template slot-scope="scope">
    <span v-if="!(scope.row.tipOff && scope.row.tipOff == 1)" style="font-size: 14px;"
      :title="'驳回理由'">
      {{scope.row.revokeReason || scope.row.platRevokeReason}}
    </span>
    <br>
    <span v-if="(scope.row.platStatus != '21' || scope.row.platStatus != '22' || scope.row.platStatus != '23') && (!scope.row.tipOff) &&
      scope.row.suggest_status && scope.row.suggest_status == 1"
      :title="'建议'" class="reviewReason">
      {{ scope.row.suggestions }}
      <br>
      <span style="color:#6A9955">{{scope.row.suggestedtime}}</span>
    </span>
    <span v-if="scope.row.tipOff && scope.row.tipOff == 1"
    :title="'举报理由'">
      {{scope.row.tipOffReason}}
    </span>
  </template>
</el-table-column>
<el-table-column
  fixed="right"
  :label="$t('table.actions')"
  align="center"
  width="180"
  class-name="small-padding fixed-width"
>
  <template slot-scope="scope">
    <el-button
      v-if="(scope.row.suggest_status == 1 || scope.row.status == 3) &&
      (tableQuery.position != 1 && tableQuery.position != 2) && 
      (formQuery.status =='3')"
      :disabled="scope.row.type == 4"
      size="mini"
      type="text"
      @click="handleEditContent(scope.row, scope.row.platStatus == 3 ? 0 : 1)"
    >{{$t('table.edit')}}
    </el-button>
    <el-button
      v-if="(scope.row.suggest_status != 1 && scope.row.status != 3) &&
      (tableQuery.position != 1 && tableQuery.position != 2) && 
      (formQuery.status =='3' || formQuery.status =='4')"
      :disabled="scope.row.type == 4"
      size="mini"
      type="text"
      @click="handleEditContent(scope.row,0)"
    >{{$t('table.edit')}}
    </el-button>
    <el-button
      v-if="scope.row.delete !='2' && tableQuery.position != 3"
      :disabled="scope.row.platStatus =='4' || scope.row.status =='4'"
      size="mini"
      type="text"
      @click="handleModifyStatus(scope.row.id)"
    >删除
    </el-button>
    <el-button
      v-if="(tableQuery.position == 3 && (scope.row.status == '0' || scope.row.status == '3')) && (formQuery.status !='4' || formQuery.status !='0')"
      :disabled="scope.row.delete !='0' || scope.row.platStatus =='4' || scope.row.status =='4'"
      size="mini"
      type="text"
      @click="handleModifyStatus(scope.row.id)"
    >删除
    </el-button>
    <el-button
      style="min-width:80px;"
      v-if="(tableQuery.position == 3 && 
        (scope.row.status != '0' && scope.row.status != '3')) && 
        (formQuery.status !='4' || formQuery.status !='0')"
      :disabled="scope.row.delete !='0' || scope.row.platStatus =='4' || scope.row.status =='4'"
      size="mini"
      type="text"
      @click="handleModifyStatus(scope.row.id,1)"
    >{{scope.row.delete =='2' ? '已申请删除':'申请删除'}}
    </el-button>
    <el-button
      v-if="tableQuery.position != 3 && scope.row.delete =='2'"
      :disabled="scope.row.platStatus =='4' || scope.row.status =='4'"
      size="mini"
      type="text"
      @click="handleModifyStatus(scope.row.id,2)"
    >同意删除
    </el-button>
    <el-button
      v-if="tableQuery.position != 3 && scope.row.delete =='2'"
      :disabled="scope.row.platStatus =='4' || scope.row.status =='4'"
      size="mini"
      type="text"
      @click="handleModifyStatus(scope.row.id,3)"
    >拒绝删除
    </el-button>
    <el-dropdown v-if="formQuery.status == '1'" @command="handleModifyMore" >
      <span class="el-dropdown-link" style="font-size: 10px;">
        更多<i class="el-icon-arrow-down el-icon--right"></i>
      </span>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item :command="scope.row.artId+'/look'">查看数据</el-dropdown-item>
        <el-dropdown-item :command="scope.row.artId">查看链接</el-dropdown-item>
        <el-dropdown-item v-if="scope.row.status == '2' && scope.row.isSchedule == 1" :command="scope.row">取消定时</el-dropdown-item>
        
      </el-dropdown-menu>
    </el-dropdown>
  </template>
</el-table-column>