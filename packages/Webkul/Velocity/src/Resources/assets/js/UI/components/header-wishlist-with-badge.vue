<template>
    <a class="wishlist-btn unset" :href="src">
        <i class="material-icons fs14" style="font-size: 14px;" >favorite_border</i>

        <div class="badge-container" v-if="wishlistCount > 0">
            <span class="fs13 badge" style="font-size: 12px;" v-text="wishlistCount"></span>
        </div>

        <span v-text="__('header.wishlist')" style="font-size: 12px;" v-if="isText == 'true'"></span>
    </a>
</template>

<script type="text/javascript">
export default {
    props: ['isCustomer', 'isText', 'src'],

    data: function() {
        return {
            wishlistCount: 0
        };
    },

    watch: {
        '$root.headerItemsCount': function() {
            this.updateHeaderItemsCount();
        }
    },

    created: function() {
        this.updateHeaderItemsCount();
    },

    methods: {
        updateHeaderItemsCount: function() {
            if (this.isCustomer == 'true') {
                this.$http
                    .get(`${this.$root.baseUrl}/items-count`)
                    .then(response => {
                        this.wishlistCount =
                            response.data.wishlistedProductsCount;
                    })
                    .catch(exception => {
                        console.log(this.__('error.something_went_wrong'));
                    });
            }
        }
    }
};
</script>
