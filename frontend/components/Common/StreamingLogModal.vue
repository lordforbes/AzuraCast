<template>
    <modal
        id="logs_modal"
        ref="$modal"
        size="xl"
        :title="$gettext('Log Viewer')"
        no-enforce-focus
        @hidden="clearContents"
    >
        <template v-if="logUrl">
            <streaming-log-view
                v-if="isStreaming"
                ref="$logView"
                :log-url="logUrl"
            />
            <fixed-log-view
                v-else
                ref="$logView"
                :log-url="logUrl"
            />
        </template>

        <template #modal-footer>
            <button
                class="btn btn-secondary"
                type="button"
                @click="hide"
            >
                {{ $gettext('Close') }}
            </button>
            <button
                class="btn btn-primary btn_copy"
                type="button"
                @click.prevent="doCopy"
            >
                {{ $gettext('Copy to Clipboard') }}
            </button>
        </template>
    </modal>
</template>

<script setup lang="ts">
import StreamingLogView from "~/components/Common/StreamingLogView.vue";
import {ref, useTemplateRef} from "vue";
import {useClipboard} from "@vueuse/core";
import Modal from "~/components/Common/Modal.vue";
import FixedLogView from "~/components/Common/FixedLogView.vue";
import {useHasModal} from "~/functions/useHasModal.ts";

const logUrl = ref<string>('');
const isStreaming = ref<boolean>(true);

const $modal = useTemplateRef('$modal');
const {show: showModal, hide} = useHasModal($modal);

const $logView = useTemplateRef('$logView');

const show = (newLogUrl: string, newIsStreaming: boolean = true) => {
    logUrl.value = newLogUrl;
    isStreaming.value = newIsStreaming;
    showModal();
};

const clipboard = useClipboard();

const doCopy = () => {
    void clipboard.copy($logView.value?.getContents());
};

const clearContents = () => {
    logUrl.value = '';
}

defineExpose({
    show
})
</script>
