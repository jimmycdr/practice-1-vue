<template>
    <div class="container">
        <h1>{{ title }}</h1>
        <div class="d-flex justify-content-between align-items-center mb-3">
            <h1 class="h3 mb-0">{{ title }}</h1>
            <button type="button" class="btn btn-success" @click="openNewModal">
                <i class="bi bi-plus-lg me-1"></i> Add Contact
            </button>
        </div>


        <div class="card shadow-sm mb-4 border-0">
            <div class="card-body">
                <div class="d-flex justify-content-between align-items-center mb-3">
                    <h5 class="mb-0"><i class="bi bi-funnel-fill me-2 text-primary"></i>Filters</h5>
                </div>
                <div class="row g-3">
                    <div class="col-md-6">
                        <div class="input-group">
                            <span class="input-group-text bg-white border-end-0">
                                <i class="bi bi-person"></i>
                            </span>
                            <input type="text" v-model="searchByName" class="form-control border-start-0"
                                placeholder="Filter by name" />
                        </div>
                    </div>
                    <div class="col-md-6">
                        <div class="input-group">
                            <span class="input-group-text bg-white border-end-0">
                                <i class="bi bi-envelope"></i>
                            </span>
                            <input type="text" v-model="searchByEmail" class="form-control border-start-0"
                                placeholder="Filter by email" />
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="table-responsive">
            <table class="table table-hover align-middle text-center">
                <thead class="table-light">
                    <tr>
                        <th>ID</th>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Address</th>
                        <th>Phone</th>
                        <th>Country</th>
                        <th>City</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in filteredItems" :key="item.id">
                        <td>{{ item.id }}</td>
                        <td>{{ item.name }}</td>
                        <td class="text-truncate" style="max-width: 180px;">{{ item.email }}</td>
                        <td>{{ item.address }}</td>
                        <td>{{ item.phone }}</td>
                        <td>{{ item.country }}</td>
                        <td>{{ item.city }}</td>
                        <td>
                            <button class="btn btn-sm btn-outline-primary me-1" @click="openEditModal(index)">
                                <i class="bi bi-pencil"></i>
                            </button>
                            <button class="btn btn-sm btn-outline-danger" @click="confirmRemoveItem(index)">
                                <i class="bi bi-trash"></i>
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

        <div class="modal fade" id="modalAutoEditor" tabindex="-1" aria-labelledby="modalAutoEditorLabel"
            aria-hidden="true" ref="modalRef">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 v-if="modalMode === 'edit'" class="modal-title" id="modalAutoEditorLabel">Edit Contact</h5>
                        <h5 v-else class="modal-title" id="modalAutoEditorLabel">Add New Contact</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <AddEditForm v-if="modalMode === 'edit' && selectedContact" :contact="selectedContact"
                            @update="saveEdit" @cancel="closeModal" />
                        <AddEditForm v-if="modalMode === 'create'" @created="addNew" @cancel="closeModal" />


                    </div>
                </div>
            </div>
        </div>
        <ConfirmModal v-if="showConfirm" :title="'Delete Contact'"
            :message="'Are you sure you want to delete this contact?'" @confirm="removeItem"
            @cancel="showConfirm = false" />
    </div>
</template>

<script>
import AddEditForm from '../components/AddEditForm.vue';
import ConfirmModal from '../components/ConfirmModal.vue';

export default {
    name: 'ContactView',
    components: {
        AddEditForm,
        ConfirmModal,
    },
    data() {
        return {
            title: 'Contact List',
            items: [
                {
                    id: 1,
                    name: "Alice Johnson",
                    email: "alice.johnson@example.com",
                    address: "123 Maple Street",
                    phone: "123-456-7890",
                    country: "USA",
                    city: "New York"
                },
                {
                    id: 2,
                    name: "Bob Smith",
                    email: "bob.smith@example.com",
                    address: "456 Oak Avenue",
                    phone: "987-654-3210",
                    country: "Canada",
                    city: "Toronto"
                },
                {
                    id: 3,
                    name: "Carol White",
                    email: "carol.white@example.com",

                    address: "789 Pine Road",
                    phone: "555-123-4567",
                    country: "UK",
                    city: "London"
                },
                {
                    id: 4,
                    name: "David Brown",
                    email: "david.brown@example.com",
                    address: "321 Elm Street",
                    phone: "444-555-6666",
                    country: "Australia",
                    city: "Sydney"
                },
                {
                    id: 5,
                    name: "Emily Davis",
                    email: "emily.davis@example.com",
                    address: "654 Spruce Lane",
                    phone: "333-444-5555",
                    country: "USA",
                    city: "Los Angeles"
                }
            ],
            searchByName: '',
            searchByEmail: '',
            searchModel: '',
            selectedContact: null,
            selectedIndex: null,
            modalInstance: null,
            modalMode: 'create',
            showConfirm: false,
            selectedIndexContact: null,
        };
    },
    mounted() {
        this.$nextTick(() => {
            if (this.$refs.modalRef) {
                this.modalInstance = new bootstrap.Modal(this.$refs.modalRef);
            } else {
                console.error('Modal reference not found');
            }
        });
    },
    computed: {
        filteredItems() {
            let results = [...this.items];

            if (this.searchByName) {
                results = results.filter(item => item.name.toLowerCase() === this.searchByName.toLowerCase().trim());
            }

            if (this.searchByEmail) {
                results = results.filter(item => item.email.toLowerCase() === this.searchByEmail.toLowerCase().trim());
            }

            return results;
        }
    },
    methods: {
        openNewModal() {
            this.modalMode = 'create';
            if (this.modalInstance) this.modalInstance.show();
        },
        openEditModal(index) {
            this.selectedIndex = index;
            this.modalMode = 'edit';
            this.selectedContact = null;

            setTimeout(() => {
                this.selectedContact = { ...this.items[index] };
                if (this.modalInstance) this.modalInstance.show();
            });
        },
        closeModal() {
            this.updatedContact = null
            if (this.modalInstance) this.modalInstance.hide();
        },
        saveEdit(updatedContact) {
            if (this.selectedIndex !== null) {
                this.items[this.selectedIndex] = updatedContact;
                this.closeModal();
            }
        },
        confirmRemoveItem(index) {
            this.showConfirm = true;
            this.selectedIndexContact = index
        },
        removeItem() {
            if (this.selectedIndexContact !== null) {
                this.items.splice(this.selectedIndexContact, 1);
                this.selectedIndexContact = null;
                this.showConfirm = false;
            }
        },
        addNew(newItem) {
            newItem.id = this.nextId()
            this.items.push(newItem);
            this.closeModal();
        },
        nextId() {
            if (this.items.length === 0) return 1;

            const sorted = [...this.items].sort((a, b) => a.id - b.id);
            return sorted[sorted.length - 1].id + 1;
        }
    }
};
</script>

<style></style>
