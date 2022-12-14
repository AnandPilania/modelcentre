<template>
    <div class="outer-assets-container">
        <div class="video-container" v-if="currentType == 'video'">
            <video :key="activeImageVideoURL" width="100%" controls>
                <source :src="activeImageVideoURL" type="video/mp4" />
            </video>
        </div>

        <div class="image-container" v-else>
            <div class="magnifier" :style="currentType != 'image360' ? 'display: block' : 'display: none'">
                <img
                    :src="activeImageVideoURL"
                    :data-zoom-image="activeImageVideoURL"
                    :class="[
                        ! isMobile()
                            ? 'main-product-image'
                            : 'vc-small-product-image',
                    ]"
                />
            </div>
            <div class="image360" :style="currentType == 'image360' ? 'display: block' : 'display: none'">
            </div>
        </div>
    </div>
</template>

<style lang="scss">
.outer-assets-container {
    width: 100%;
    .img360 {
        width: 640px;
    }
    .image-container {
        .magnifier {
            > img {
                width: 100%;
            }
        }
    }

    @media only screen and (max-width: 992px) {
        .image-container {
            margin: 0 auto;
            .img360 {
                width: 100%;
            }
            .magnifier {
                > img {
                    width: 100%;
                }
            }
        }
    }

    .video-container {
        position: relative;
        min-height: 420px;
        max-height: 420px;

        video {
            max-height: 420px;
        }
    }
}
</style>

<script type="text/javascript">
export default {
    props: ['src', 'type'],

    data: function () {
        return {
            activeImage: null,
            activeImageVideoURL: this.src,
            currentType: this.type,
        };
    },

    mounted: function () {
        /* binding should be with class as ezplus is having bug of creating multiple containers */
        this.activeImage = $('.main-product-image');
        this.activeImageVideoURL = JSON.parse(this.activeImageVideoURL)
        if(this.currentType != "image360"){
            this.activeImage.attr('src', this.activeImageVideoURL);
            this.activeImage.data('zoom-image', this.activeImageVideoURL);
            this.elevateZoom()
        }else {
            this.activeImage.attr('src', this.activeImageVideoURL[0]);
            this.activeImage.data('zoom-image', this.activeImageVideoURL[0]);
            this.elevateZoom()
            this.make360()
        }

        this.$root.$on(
            'changeMagnifiedImage',
            ({ smallImageUrl, largeImageUrl, currentType }) => {
                if(currentType == "image360") {
                    $('.zoomContainer').remove();
                    this.activeImage.removeData('elevateZoom');
                    this.activeImageVideoURL = JSON.parse(largeImageUrl);
                    this.currentType = currentType;
                    this.make360();
                }else {
                    console.log("changed")
                    /* removed old instance */
                    $('.zoomContainer').remove();
                    this.activeImage.removeData('elevateZoom');

                    /* getting url */
                    this.activeImageVideoURL = JSON.parse(largeImageUrl);

                    /* type checking for media type */
                    this.currentType = currentType;

                    /* waiting added for image because image element takes time load when switching from video  */
                    this.waitForElement('.main-product-image', () => {
                        /* update source for images */
                        this.activeImage = $('.main-product-image');
                        this.activeImage.attr('src', smallImageUrl);
                        this.activeImage.data('zoom-image', JSON.parse(largeImageUrl));

                        /* reinitialize zoom */
                        this.elevateZoom();
                    });
                }
            }
        );
    },

    methods: {
        elevateZoom: function () {
            $(".magnifier").css({cursor: 'point'})
            console.log(this.activeImage)
            this.activeImage.ezPlus({
                zoomLevel: 0.5,
                cursor: 'pointer',
                scrollZoom: true,
                zoomWindowWidth: 300,
                zoomWindowHeight: 300,
            });
        },
        make360: function() {
            $(".image360").tikslus360({
                imageDir: this.activeImageVideoURL,
                imageCount: this.activeImageVideoURL.length > 36 ? 36 : this.activeImageVideoURL.length,
                imageExt:'jpg',
                canvasID:'mycar',
                canvasWidth: 640,
                canvasHeight: 480,
                autoRotate: false
            });
        },

        waitForElement: function (selector, callback) {
            if (jQuery(selector).length) {
                callback();
            } else {
                setTimeout(() => {
                    this.waitForElement(selector, callback);
                }, 100);
            }
        },
    },
};
</script>
