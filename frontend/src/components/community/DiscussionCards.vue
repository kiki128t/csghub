<template>
  <div class="flex flex-col gap-[16px]">
    <div class="flex justify-between">
        <div @click="showDetail?showDetail=!showDetail:changeFlag('new')" class="px-[16px] py-[5px] border bg-gray-25 rounded-xs hover:bg-gray-100 text-gray-500 text-sm leading-[22px] cursor-pointer ">
          {{ showDetail? $t('community.discussion.back'):$t('community.discussion.new') }}
        </div>
        <!-- <div class="hover:bg-gray-100 cursor-pointer flex items-center gap-[8px] px-[16px] py-[5px] rounded-xs text-gray-500 text-sm leading-[22px]">
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 14 14" fill="none"><path d="M5.54175 7.87533L7.00008 6.41699M7.00008 6.41699L8.45841 4.95866M7.00008 6.41699L5.54175 4.95866M7.00008 6.41699L8.45841 7.87533M10.918 12.2678V12.2678C11.761 12.7735 12.8334 12.1663 12.8334 11.1833V7.00032C12.8334 5.29289 12.8334 4.43917 12.577 3.75799C12.1713 2.67993 11.3205 1.82912 10.2424 1.42337C9.56124 1.16699 8.70752 1.16699 7.00008 1.16699H6.41675C5.25451 1.16699 4.6734 1.16699 4.19483 1.28687C2.76313 1.64549 1.64525 2.76337 1.28662 4.19507C1.16675 4.67364 1.16675 5.25476 1.16675 6.41699V6.41699C1.16675 7.57923 1.16675 8.16034 1.28662 8.63891C1.64525 10.0706 2.76313 11.1885 4.19483 11.5471C4.6734 11.667 5.25451 11.667 6.41675 11.667H8.74907C9.51314 11.667 10.2628 11.8746 10.918 12.2678Z" stroke="#606266" stroke-linecap="round" stroke-linejoin="round"/></svg>
          View closed（1）
        </div> -->
    </div>
    <EmptyCommunity v-if="theCards.length <= 0" @changeFlag="changeFlag" />
    <template v-else>
      <DiscussionDetails v-if="showDetail"
        :discussionId="currentDiscussion"
        :title="currentTitle"
        :userName="currentUserName"
        :createUserId="createUserId"
        :time="currentTime"
        @getDiscussion="getDiscussion"
        @toggleDetails="toggleDetails" />
      <DiscussionCard v-else v-for="card in theCards" @click="showDetails(card)"
        :key="card.id"
        :discussionId="card.id"
        :num="card.num"
        :title="card.title"
        :time="formatDate(card.created_at)"
        :user="card.user.name"
        :commentNum="card.commentNum"/>
    </template>
  </div>
</template>

<script>
  import EmptyCommunity from "./EmptyCommunity.vue"
  import DiscussionCard from "./DiscussionCard.vue"
  import DiscussionDetails from "./DiscussionDetails.vue"
  import dayjs from 'dayjs'

  export default {
    props: {
      cards: Array
    },
    components: {
      DiscussionCard,
      DiscussionDetails,
      EmptyCommunity
    },
    data() {
      return {
        theCards: this.cards,
        showDetail: false,
        currentDiscussion: ''
      }
    },
    watch: {
      cards(newCards, _) {
        this.theCards = newCards
      }
    },
    mounted() {},
    methods: {
      formatDate (date) {
        if (!date) {
          console.warn("Invalid date provided to formatDate:", date);
          return ""; // Return an empty string or a default value
        }
        return dayjs(date).format('YYYY-MM-DD HH:mm:ss');
      },
      showDetails(card) {
        this.currentDiscussion = card.id
        this.currentTitle = card.title
        this.currentUserName = card.user.name
        this.createUserId = card.user.id
        this.currentTime = this.formatDate(card.created_at)
        this.showDetail = true
      },
      toggleDetails() {
        this.showDetail = false
      },
      changeFlag(flag){
        this.$emit('changeFlag',flag)
      },
      getDiscussion(){
        this.$emit("getDiscussion")
      }
    }
  }
</script>
