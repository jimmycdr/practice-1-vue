<template>
    <div class="modal fade show" tabindex="-1" style="display: block; background-color: rgba(0, 0, 0, 0.5)"
        role="dialog" aria-modal="true">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content border-0 shadow-lg rounded-3">
                <div class="modal-header">
                    <h5 class="modal-title">{{ title }}</h5>
                    <button type="button" class="btn-close" @click="$emit('cancel')" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <p>{{ message }}</p>
                </div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" @click="$emit('cancel')">Cancel</button>
                    <button class="btn btn-danger" @click="$emit('confirm')">Yes, Confirm</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ConfirmModal',
    props: {
        title: {
            type: String,
            default: 'Are you sure?'
        },
        message: {
            type: String,
            default: 'Are you sure?'
        }
    }
};
</script>
