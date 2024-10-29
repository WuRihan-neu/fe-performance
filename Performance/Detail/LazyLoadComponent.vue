<template>
    <div ref="observer">
        <slot v-if="isVisible" :is-visible="isVisible"></slot>
    </div>
</template>

<script>
import {throttle} from 'lodash';

/**
 * 使用说明
 * 1. 异步加载比较大的组件
 * 包裹内容是异步加载的组件，此种适用于组件比较大的情况，示例如下
 * const xxComp = () => import('@components/xx.vue)
 * <lazy-load-component>
 *     // 要用isVisible控制显隐，否则刚开始时组件js会被加载，只是没有显示
 *     <xx-comp slot-scope="{isVisible}" v-if="isVisible"/>
 * </lazy-load-component>
 *
 * 2. 包裹的内容比较小，只是异步加载图片或者按需请求后端接口，无需异步加载组件代码，可以如下使用
 * import xxComp from '@components/Home/xxComp.vue';
 * <lazy-load-component>
 *     <xx-comp/>
 * </lazy-load-component>
 *
 * 3. 包裹的内容可以是任何内容，不仅仅是组件，也可以是多个组件，能合并的尽量合并，避免滚动事件监听太多，造成页面卡顿
 */
export default {
    name: 'LazyLoadComponent',
    data() {
        return {
            isVisible: false,
            observer: null
        };
    },
    mounted() {
        /*
        // 使用IntersectionObserver多个组件会同时触发显示，有问题
        if (window.IntersectionObserver) {
            this.observer = new IntersectionObserver(
                ([entry]) => {
                    if (entry && entry.isIntersecting) {
                        this.isVisible = true;
                        this.observer.disconnect();
                    }
                },
                {
                    threshold: 0.1,
                }
            );
            this.observer.observe(this.$refs.observer);
        } else {
            window.addEventListener('scroll', this.checkIsScrollToViewport)
        }*/
        window.addEventListener('scroll', this.checkIsScrollToViewport);
    },
    beforeDestroy() {
        /*if (this.observer) {
            this.observer.disconnect();
        } else {
            window.removeEventListener('scroll', this.checkIsScrollToViewport)
        }*/
        window.removeEventListener('scroll', this.checkIsScrollToViewport);
    },
    methods: {
        checkIsScrollToViewport: throttle(
            function () {
                const {top} = this.$refs.observer.getBoundingClientRect();
                const clientHeight = document.documentElement.clientHeight;
                if (!this.isVisible && top <= clientHeight) {
                    this.isVisible = true;
                    window.removeEventListener('scroll', this.checkIsScrollToViewport);
                }
            },
            100,
            {trailing: true}
        )
    }
};
</script>

<style scoped></style>
