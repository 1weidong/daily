<template>
    <div class="daily">
        <div class="daily-meun">
            <div class="daily-meun-item"
                @click="handleToRecommend"
                :class="{on: type=== 'recommend'}">每日推荐</div>
            <div class="daily-meun-item"
                :class='{on: type=== "daily"}'
                @click="showThemes = !showThemes">主题日报</div>
            <ul v-show="showThemes">
                <li v-for="item in themes">
                    <a
                        :class="{on: item.id === themeId && type === 'daily'}"
                        @click="handleToTheme(item.id)">{{ item.name }}</a>
                </li>
            </ul>
        </div>
        <div class="daily-list">
            <template v-if="type === 'recommend'">
                <div v-for="list in recommendList">
                    <div class="daily-date">{{ formatDay(list.date) }}</div>
                    <Item
                        v-for="item in list.stories"
                        :data="item"
                        :key="item.id"></Item>
                </div>
            </template>
            <template v-if="type === 'daily'">
                <Item
                    v-for="item in list"
                    :data="item"
                    :key="item.id">
                </Item>
            </template>
        </div>
        <!--<daily-ariticle></daily-ariticle>-->
    </div>
</template>
<script>
    import $ from './libs/util';
    import Item from './components/item.vue';
    export default {
        components: { Item },
        data () {
            return {
                themes: [],
                recommendList: [],
                list: [],
                dailyTime: $.getTodayTime(),
                showThemes: false,
                type: 'recommend',
                themeId: 0
            }
        },
        methods: {
            getThemes () {
                //ajax发起get请求
                $.ajax.get('themes').then(res => {
                    this.themes = res.others;
                })
            },
            handleToTheme (id) {
                //改变菜单分类
                this.type = 'daily';
                //设置当前点击子类的的主题日报id
                this.themeId = id;
                //清空中间栏的数据
                this,list = [];
                $.ajax.get('theme/'+is).then(res => {
                    //过滤掉类型为1的文章，该类型下的文章为空
                    this.list = res.stories
                        .filter(item => item.type !==1);
                })
            },
            handleToRecommend () {
                this.type = 'recommend';
                this.recommondList = [];
                this.dailyTime = $.getTodayTime();
                this.getRecommendList();
            },
            getRecommendList () {
                this.isLoading = true;
                const prevDay = $.prevDay(this.dailyTime + 86400000);
                $.ajax.get('news/before' + prevDay).then(res => {
                    this.getRecommendList.push(res);
                    this.isLoading = false;
                })
            },
            methods: {
                //转换为带汉字的月日
                formatDay (date) {
                    let month = date.substr(4, 2);
                    let day = date.substr(6, 2);
                    if (month.substr(0, 1) === '0') month = month.substr(1, 1);
                    if (day.substr(0, 1) === '0') day = day.substr(1, 1);
                    return '${month} 月 ${day} 日';
                }
            }
        },
        mounted () {
            //初始化时调用
            this.getThemes();
            this.getRecommendList();
        }
    }
</script>
<style>

</style>