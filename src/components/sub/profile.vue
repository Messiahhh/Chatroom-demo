<style lang="stylus" scoped>
@import '../../assets/css/global.styl'
section
    height 100%
    overflow auto
    padding 0 30px
    background greyColor
    border-right 1px solid themeColor
    .image
        width 200px
        height 200px
        border-radius 50%
        margin 25px auto
        border 5px solid #fff
        background-repeat no-repeat
        background-size cover
        .changeImage
            width 100%
            height 100%
            border-radius 50%
            position relative
            input
                width 100%
                height 100%
                opacity 0
                position relative
                cursor pointer
            i
                position absolute
                top 50%
                left 50%
                transform translate(-50%, -50%)
                font-size 32px
                color #fff
                display none
        &:hover .changeImage
            border 5px solid rgb(231, 98, 129)
            background rgba(0, 0, 0, .45)
            cursor pointer
            i
                display block
    .infoPage
        width 220px
        margin 10px auto
        display flex
        flex-direction column
        .info
            flex-basis 50px
            line-height 50px
            padding-left 15px

        .resume
            flex-basis 120px
            padding-left 15px
            div
                height 50px
                line-height 50px
            span
                font-size 14px
                color #789
    .editPage
        width 220px
        margin 10px auto
</style>

<template lang="html">
    <section>
        <div class="image" :style="{backgroundImage: 'url(' + imgUrl + ')'}">
            <div class="changeImage" v-if='isEditing'>
                <input type="file" class='upload' @change="changeFile">
                <i class='el-icon-picture'></i>
            </div>
        </div>
        <template v-if='!isEditing'>
            <div class="infoPage" v-loading="isLoading">
                <div class="info">
                    昵称&#160;&#160;&#160;{{info.usr}}
                </div>
                <div class="info">
                    性别&#160;&#160;&#160;{{info.gender}}
                </div>
                <div class="info">
                    生日&#160;&#160;&#160;{{info.birth}}
                </div>
                <div class="info">
                    城市&#160;&#160;&#160;{{info.city}}
                </div>
                <div class="resume">
                    <div class="">
                        个性签名
                    </div>
                    <span>{{info.resume}}</span>
                </div>
            </div>
            <el-button v-if="usr === user.usr" type='primary' @click='changeState' style="margin: 0 auto;display: block;">修改个人资料</el-button>
        </template>
        <template v-else>
            <div class="editPage">
                <el-form>
                    <el-form-item label="用户名">
                        <el-input v-model="editingInfo.usr"></el-input>
                    </el-form-item>
                    <el-form-item label="性别">
                        <el-radio v-model="editingInfo.gender" label="男">男生</el-radio>
                        <el-radio v-model="editingInfo.gender" label="女">女生</el-radio>
                    </el-form-item>
                    <el-form-item label="生日">
                        <el-date-picker
                          v-model="editingInfo.birth"
                          type="date"
                          value-format="yyyy-MM-dd"
                          placeholder="选择日期">
                        </el-date-picker>
                    </el-form-item>
                    <el-form-item label="城市">
                        <el-select v-model="editingInfo.city">
                            <el-option v-for="city in cities" :value="city" :key="city"></el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="个性签名">
                        <el-input type='textarea' :autosize="{minRows: 2}" resize="none" placeholder="这个人很懒，什么也没说" v-model="editingInfo.resume"></el-input>
                    </el-form-item>
                    <el-button type='primary' @click='uploadImage'>完成</el-button>
                    <el-button type='primary' @click='backState' plain>撤销</el-button>
                </el-form>
            </div>
        </template>
    </section>
</template>

<script>
import axios from 'axios'
export default {
    props: ['usr'],
    data() {
        return {
            isEditing: false,
            isLoading: false,
            previewUrl: '',
            info: {},
            editingInfo: {},
            cities:
                [
                    '北京市',
                    '天津市',
                    '河北省',
                    '山西省',
                    '内蒙古自治区',
                    '辽宁省',
                    '吉林省',
                    '黑龙江省',
                    '上海市',
                    '江苏省',
                    '浙江省',
                    '安徽省',
                    '福建省',
                    '江西省',
                    '山东省',
                    '河南省',
                    '湖北省',
                    '湖南省',
                    '广东省',
                    '广西壮族自治区',
                    '海南省',
                    '重庆市',
                    '四川省',
                    '贵州省',
                    '云南省',
                    '西藏自治区',
                    '陕西省',
                    '甘肃省',
                    '青海省',
                    '宁夏回族自治区',
                    '新疆维吾尔自治区',
                ]


        }
    },

    computed: {
        user() {
            return this.$store.state.user
        },

        imgUrl() {
            if (this.previewUrl === '' && this.info.imgUrl) {
                return this.info.imgUrl
            }
            else {
                return this.previewUrl
            }
        }

    },

    methods: {
        changeState() {
            this.isEditing = !this.isEditing
        },

        backState() {
            this.changeState()
            this.previewUrl = ''
        },

        changeFile(e) {
            let file = e.target.files[0]
            let reader = new FileReader()
            reader.addEventListener('load', () => {
                this.previewUrl = reader.result
            })
            reader.readAsDataURL(file)
        },

        async uploadImage() {
            let input = document.querySelector('.upload')
            let file = input.files[0]
            let fd = new FormData()
            fd.append('oldUsr', this.info.usr)
            if (file) {
                fd.append('file', file)
            }
            if (this.info.usr !== this.editingInfo.usr)
                fd.append('newUsr', this.editingInfo.usr)
            if (this.info.gender !== this.editingInfo.gender)
                fd.append('gender', this.editingInfo.gender)
            if (this.info.birth !== this.editingInfo.birth)
                fd.append('birth', this.editingInfo.birth)
            if (this.info.city !== this.editingInfo.city)
                fd.append('city', this.editingInfo.city)
            if (this.info.resume !== this.editingInfo.resume)
                fd.append('resume', this.editingInfo.resume)
            let res = await axios({
                url: '/upload',
                method: 'post',
                data: fd,
            })
            if (res.data.status === 200) {
                if (res.data.changeImg) {
                    this.$cookie.set('imgUrl', res.data.imgUrl)
                }
                location.reload()
            }
        },

        async getInfo () {
            let usr = this.usr
            this.isLoading = true
            let res = await axios({
                method: 'post',
                url: '/getInfo',
                data: {
                    usr
                }
            })
            this.info = Object.assign({ usr }, {...res.data})
            this.editingInfo = Object.assign({ usr }, {...res.data})
            this.isLoading = false
        }

    },

    watch: {
        '$route' (to, from) {
            console.log(to, from);
            this.getInfo()
        }
    },
    async created() {
        this.getInfo()
    }
}
</script>
