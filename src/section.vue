<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="navbar_top_absolute" :class="{'block': editMode}" ww-fixed>
        <div class="navbar-top">
            <div class="container">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" position="center" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->

                <!-- BACKGROUND -->
                <wwObject class="background" :ww-object="section.data.background" ww-category="background"></wwObject>

                <!-- MENU -->
                <wwLayoutColumn tag="div" ww-default="ww-row" :ww-list="section.data.menu" class="content" @ww-add="add(section.data.menu, $event)" @ww-remove="remove(section.data.menu, $event)">
                    <wwObject v-for="item in section.data.menu" :key="item.uniqueId" :ww-object="item"></wwObject>
                </wwLayoutColumn>
            </div>
        </div>
        <!-- MOBILE SIDEBAR -->
        <div class="navbar-side">
            <div class="container">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->
                <wwObject class="background" :ww-object="section.data.backgroundSide" ww-category="background"></wwObject>

                <wwLayoutColumn tag="div" ww-default="ww-row" :ww-list="section.data.menuSide" class="content" @ww-add="add(section.data.menuSide, $event)" @ww-remove="remove(section.data.menuSide, $event)">
                    <wwObject v-for="item in section.data.menuSide" :key="item.uniqueId" :ww-object="item"></wwObject>
                </wwLayoutColumn>
            </div>
        </div>
        <div class="navbar-cover" :class="{'show': navbarOpen}" @click="toggleNavbar"></div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>

export default {
    name: "__COMPONENT_NAME__",
    props: {
        // The section controller object is passed to you.
        sectionCtrl: Object
    },
    data() {
        return {
            navbarOpen: false
        }
    },
    computed: {
        section() {
            return this.sectionCtrl.get();
        },
        editMode() {
            return this.sectionCtrl.getEditMode() == 'CONTENT'
        }
    },
    methods: {
        init() {
            wwLib.$on('wwNavbar:toggle', this.toggleNavbar);
        },

        /*=============================================m_ÔÔ_m=============================================\
          TOGGLE NAVBAR SIDE
        \================================================================================================*/
        toggleNavbar() {
            this.navbarOpen = !this.navbarOpen;
            if (this.navbarOpen) {
                for (let section of document.querySelectorAll('.ww-section:not([ww-fixed])')) {
                    section.classList.add('navbar_top_absolute-open');
                }
                for (let container of this.$el.querySelectorAll('.container')) {
                    container.classList.add('navbar_top_absolute-open');
                }
            }
            else {
                for (let section of document.querySelectorAll('.ww-section:not([ww-fixed])')) {
                    section.classList.remove('navbar_top_absolute-open');
                }
                for (let container of this.$el.querySelectorAll('.container')) {
                    container.classList.remove('navbar_top_absolute-open');
                }
            }
        },

        /*=============================================m_ÔÔ_m=============================================\
          ADD / REMOVE ROWS
        \================================================================================================*/
        /* wwManager:start */
        add(list, options) {
            list.splice(options.index, 0, options.wwObject);
            this.sectionCtrl.update(this.section);
        },
        remove(list, options) {
            list.splice(options.index, 1);
            this.sectionCtrl.update(this.section);
        },
        /* wwManager:end */
    },
    mounted() {
        this.init();
    },
    created() {

        let needUpdate = false;

        //Initialize section data
        this.section.data = this.section.data || {};


        if (!this.section.data.background) {
            this.section.data.background = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
            needUpdate = true;
        }

        if (!this.section.data.menu) {
            this.section.data.menu = [];
            needUpdate = true;
        }

        if (!this.section.data.backgroundSide) {
            this.section.data.backgroundSide = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
            needUpdate = true;
        }

        if (!this.section.data.menuSide) {
            this.section.data.menuSide = [];
            needUpdate = true;
        }

        if (needUpdate) {
            this.sectionCtrl.update(this.section);
        }

    },
    beforeDestroy() {
        wwLib.$off('wwNavbar:toggle', this.toggleNavbar);
    }
};
</script>

<style lang="scss">
.navbar_top_absolute-open {
    transform: translateX(-200px);
}

.ww-section:not([ww-fixed]) {
    transition: transform 0.3s ease;
}
</style>

<style scoped lang="scss">
$navbar-width: 330px;

.navbar_top_absolute {
    position: absolute;
    z-index: 1;
    top: -1px;
    width: 100%;
    &.block {
        position: relative;
    }

    .navbar-top {
        width: 100%;
        z-index: 101;

        .background {
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            width: 100%;
        }

        .container {
            width: 100%;
            transition: transform 0.3s ease;

            &.navbar_top_absolute-open {
                transform: translateX(-$navbar-width);
            }
        }
    }

    .navbar-side {
        position: absolute;
        top: 0;
        left: 100%;
        width: $navbar-width;
        z-index: 101;
        height: 100vh;

        .container {
            width: 100%;
            height: 100%;
            transition: transform 0.3s ease;
            &.navbar_top_absolute-open {
                transform: translateX(-$navbar-width);
            }
            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }
            .content {
                position: relative;
            }
        }
    }

    .navbar-cover {
        display: none;
        pointer-events: all;
        width: 100%;
        position: fixed;
        top: 0;
        bottom: 0;
        z-index: 100;

        &.show {
            display: block;
        }
    }
}
</style>
