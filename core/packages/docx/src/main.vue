<script>
import {defineComponent, ref, onMounted, watch} from 'vue-demi';
import docx from './docx';

export default defineComponent({
    name: 'VueOfficeDocx',
    props: {
        src: [String, ArrayBuffer, Blob],
        requestOptions: {
            type: Object,
            default: () => ({})
        },
        options:{
            type: Object,
            default: () => ({})
        }
    },
    emits: ['rendered', 'error'],
    setup(props, {emit}) {
        const rootRef = ref(null);

        function init() {
            let container = rootRef.value;
            docx.getData(props.src, props.requestOptions).then(res => {
                docx.render(res, container, props.options).then(() => {
                    emit('rendered');
                }).catch(e => {
                    docx.render('', container, props.options);
                    emit('error', e);
                });
            }).catch(e => {
                docx.render('', container, props.options);
                emit('error', e);
            });
        }

        onMounted(() => {
            if (props.src) {
                init();
            }
        });

        watch(() => props.src, () => {
            if (props.src) {
                init();
            } else {
                docx.render('', rootRef.value, props.options).then(() => {
                    emit('rendered');
                });
            }
        });
        return {
            rootRef
        };
    }
});
</script>

<template>
    <div class="vue-office-docx">
        <div class="vue-office-docx-main" ref="rootRef"></div>
    </div>
</template>

<style lang="less">
.vue-office-docx {
    height: 100%;
    overflow-y: auto;
    .docx-wrapper {
        > section.docx {
           margin-bottom: 5px;
        }
    }
}

@media screen and (max-width: 800px) {
    .vue-office-docx {
        .docx-wrapper {
            padding: 10px;

            > section.docx {
                padding: 10px !important;
                width: 100% !important;
            }
        }
    }
}

</style>