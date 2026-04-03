<template>
    <div class="page-background min-vh-100 py-5">
        <div class="container">
            <!-- Header Section -->
            <div class="row mb-5 align-items-end">
                <div class="col-md-6">
                    <h6 class="text-uppercase tracking-widest text-primary fw-bold mb-2">Inventory System</h6>
                    <h1 class="display-4 fw-black text-dark mb-0">Product Management</h1>
                </div>
                <div class="col-md-6 text-md-end mt-4 mt-md-0">
                    <button class="btn btn-dark btn-pill px-4 py-3 shadow-highlight transition-up"
                        @click="openModal('add')">
                        <i class="bi bi-plus-lg me-2"></i> Add New Product
                    </button>
                </div>
            </div>

            <!-- Global Error Alert -->
            <transition name="fade">
                <div v-if="globalError"
                    class="alert alert-danger alert-glass border-0 mb-4 d-flex align-items-center justify-content-between p-4 shadow-sm"
                    role="alert">
                    <div class="d-flex align-items-center">
                        <i class="bi bi-exclamation-octagon-fill fs-4 me-3"></i>
                        <div>
                            <strong class="d-block">System Alert</strong>
                            <span class="small">{{ globalError }}</span>
                        </div>
                    </div>
                    <button type="button" class="btn-close" @click="globalError = null"></button>
                </div>
            </transition>

            <!-- Statistics Overview (Extra Elegant Touch) -->
            <div class="row g-4 mb-5 text-start">
                <div class="col-md-4">
                    <div class="stat-card p-4 transition-up">
                        <div class="d-flex align-items-start justify-content-between">
                            <div>
                                <p class="text-muted small text-uppercase fw-bold mb-1">Total Products</p>
                                <h2 class="fw-black mb-0">{{ products.length }}</h2>
                            </div>
                            <div class="icon-circle bg-primary-subtle text-primary">
                                <i class="bi bi-box fs-4"></i>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="stat-card p-4 transition-up">
                        <div class="d-flex align-items-start justify-content-between">
                            <div>
                                <p class="text-muted small text-uppercase fw-bold mb-1">Total Stock</p>
                                <h2 class="fw-black mb-0">{{ totalStock }}</h2>
                            </div>
                            <div class="icon-circle bg-success-subtle text-success">
                                <i class="bi bi-stack fs-4"></i>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="stat-card p-4 transition-up">
                        <div class="d-flex align-items-start justify-content-between">
                            <div>
                                <p class="text-muted small text-uppercase fw-bold mb-1">Inventory Value</p>
                                <h2 class="fw-black mb-0">${{ totalValue.toLocaleString() }}</h2>
                            </div>
                            <div class="icon-circle bg-info-subtle text-info">
                                <i class="bi bi-currency-dollar fs-4"></i>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Product Table -->
            <div class="card card-premium overflow-hidden">
                <div class="table-responsive">
                    <table class="table table-hover align-middle mb-0">
                        <thead>
                            <tr>
                                <th class="table-head-cell px-4">#ID</th>
                                <th class="table-head-cell">Product Details</th>
                                <th class="table-head-cell text-end">Pricing</th>
                                <th class="table-head-cell text-center">Status & Stock</th>
                                <th class="table-head-cell text-center px-4">Management</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-if="products.length === 0">
                                <td colspan="5" class="text-center py-5 text-muted">
                                    <div class="py-5">
                                        <i class="bi bi-inbox fs-1 d-block mb-3 opacity-25"></i>
                                        <p class="fs-5 fw-light">Your inventory is currently empty.</p>
                                        <button class="btn btn-outline-primary btn-sm"
                                            @click="openModal('add')">Initialize First Product</button>
                                    </div>
                                </td>
                            </tr>
                            <tr v-for="product in products" :key="product.pro_id" class="table-row">
                                <td class="px-4">
                                    <span class="small fw-bold text-muted opacity-50">#{{ product.pro_id }}</span>
                                </td>
                                <td>
                                    <div class="d-flex flex-column">
                                        <span class="fw-bold text-dark fs-6">{{ product.pro_name }}</span>
                                        <span class="text-muted small text-truncate" style="max-width: 250px;">{{
                                            product.pro_desc }}</span>
                                    </div>
                                </td>
                                <td class="text-end">
                                    <span class="fw-black text-dark">${{ product.pro_price?.toLocaleString(undefined,
                                        { minimumFractionDigits: 2 }) }}</span>
                                </td>
                                <td class="text-center">
                                    <div class="d-flex flex-column align-items-center">
                                        <div
                                            :class="['stock-indicator mb-1', product.pro_count < 5 ? 'bg-danger' : (product.pro_count < 15 ? 'bg-warning' : 'bg-success')]">
                                        </div>
                                        <span class="small fw-bold text-secondary">{{ product.pro_count }} In
                                            Stock</span>
                                    </div>
                                </td>
                                <td class="text-center px-4">
                                    <div class="btn-group-elegant">
                                        <button class="btn-icon btn-icon-warning me-2"
                                            @click="openModal('edit', product)" title="Edit">
                                            <i class="bi bi-pencil-square"></i>
                                        </button>
                                        <button class="btn-icon btn-icon-danger" @click="confirmDelete(product)"
                                            title="Delete">
                                            <i class="bi bi-trash3"></i>
                                        </button>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Product Modal (Add/Edit) - Refined Premium Design -->
        <div class="modal fade" id="productModal" tabindex="-1" aria-hidden="true" data-bs-backdrop="static">
            <div class="modal-dialog modal-dialog-centered modal-xl">
                <div class="modal-content modal-content-premium border-0 overflow-hidden shadow-2xl rounded-5">
                    <div class="row g-0">
                        <!-- Modal Sidebar Decor (3/12 Ratio) -->
                        <div
                            class="col-lg-3 d-none d-lg-flex modal-accent bg-dark text-white p-5 flex-column justify-content-center text-center">
                            <div class="mb-4">
                                <div class="icon-shape bg-white-10 mx-auto mb-4">
                                    <i class="bi bi-box-seam fs-1 text-white"></i>
                                </div>
                                <h4 class="fw-black text-uppercase tracking-widest small mb-3">Database</h4>
                                <div class="h-px bg-white-10 w-25 mx-auto mb-4"></div>
                                <h2 class="fw-black mb-0">{{ isEdit ? 'Update' : 'Create' }}</h2>
                            </div>
                            <p class="small text-white-50 lh-lg px-2">Ensure all product specifications are captured
                                accurately for synchronized inventory records.</p>
                        </div>

                        <!-- Modal Main Content (9/12 Ratio) -->
                        <div class="col-lg-9 bg-white">
                            <div class="modal-header border-0 pb-0 px-4 px-md-5 pt-4">
                                <div class="d-flex align-items-center">
                                    <div class="bg-primary-subtle text-primary p-2 rounded-3 me-3 d-lg-none">
                                        <i class="bi bi-pencil-square"></i>
                                    </div>
                                    <h5 class="modal-title fw-black text-dark fs-4">
                                        {{ isEdit ? 'Edit Product Details' : 'Register New Product' }}
                                    </h5>
                                </div>
                                <button type="button" class="btn-close-custom ms-auto" data-bs-dismiss="modal"
                                    aria-label="Close">
                                    <i class="bi bi-x-lg"></i>
                                </button>
                            </div>

                            <div class="modal-body p-4 p-md-5 pt-4">
                                <transition name="fade">
                                    <div v-if="modalError"
                                        class="alert alert-warning-soft border-0 shadow-sm d-flex align-items-center p-3 mb-4">
                                        <i class="bi bi-exclamation-triangle-fill me-3 fs-5"></i>
                                        <span class="small fw-semibold">{{ modalError }}</span>
                                    </div>
                                </transition>

                                <form @submit.prevent="saveProduct" class="row g-4 text-start">
                                    <!-- Product Name - 100% -->
                                    <div class="col-12">
                                        <label class="form-label-elegant">Product Identification</label>
                                        <div class="input-group-elegant w-100">
                                            <span class="input-icon"><i class="bi bi-tag"></i></span>
                                            <input v-model="form.pro_name" type="text"
                                                class="form-control-elegant w-100"
                                                placeholder="e.g. High-Performance DELL Server" required>
                                        </div>
                                    </div>

                                    <!-- Price - 100% -->
                                    <div class="col-12">
                                        <label class="form-label-elegant">Unit Valuation (USD)</label>
                                        <div class="input-group-elegant w-100">
                                            <span class="input-icon"><i class="bi bi-currency-dollar"></i></span>
                                            <input v-model.number="form.pro_price" type="number" step="0.01"
                                                class="form-control-elegant w-100" placeholder="0.00" required>
                                        </div>
                                    </div>

                                    <!-- Stock - 100% -->
                                    <div class="col-12">
                                        <label class="form-label-elegant">Inventory Count</label>
                                        <div class="input-group-elegant w-100">
                                            <span class="input-icon"><i class="bi bi-hash"></i></span>
                                            <input v-model.number="form.pro_count" type="number"
                                                class="form-control-elegant w-100" placeholder="0" required>
                                        </div>
                                    </div>

                                    <!-- Description - 100% -->
                                    <div class="col-12">
                                        <label class="form-label-elegant">Product Description</label>
                                        <div class="input-group-elegant align-items-start w-100">
                                            <span class="input-icon mt-3"><i class="bi bi-text-paragraph"></i></span>
                                            <textarea v-model="form.pro_desc" class="form-control-elegant w-100"
                                                rows="3"
                                                placeholder="Briefly describe specifications, technical details, or purpose..."></textarea>
                                        </div>
                                    </div>

                                    <div class="col-12 mt-5">
                                        <div class="d-flex align-items-center gap-3">
                                            <button type="button"
                                                class="btn btn-light btn-pill px-4 py-3 border text-secondary fw-bold"
                                                data-bs-dismiss="modal" :disabled="loading">
                                                Discard
                                            </button>
                                            <button type="submit"
                                                class="btn btn-dark btn-pill flex-grow-1 py-3 shadow-lg transition-up d-flex align-items-center justify-content-center"
                                                :disabled="loading">
                                                <span v-if="loading"
                                                    class="spinner-border spinner-border-sm me-3"></span>
                                                <span class="text-uppercase tracking-widest small fw-black">
                                                    {{ isEdit ? 'Update Implementation' : 'Confirm Registration' }}
                                                </span>
                                            </button>
                                        </div>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Delete Confirmation - Refined -->
        <div class="modal fade" id="deleteModal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog modal-sm modal-dialog-centered">
                <div class="modal-content border-0 shadow-lg rounded-4 p-3">
                    <div class="modal-body text-center p-4">
                        <div class="delete-icon-ring mb-4">
                            <i class="bi bi-archive text-danger fs-2"></i>
                        </div>
                        <h5 class="fw-black mb-3">Delete Product?</h5>
                        <p class="text-muted small mb-4">Confirming this will permanently remove <br><strong
                                class="text-dark">{{ selectedProduct?.pro_name }}</strong>.</p>

                        <div v-if="modalError" class="alert alert-danger-soft small mb-4 py-2 border-0 rounded-3">
                            {{ modalError }}
                        </div>

                        <div class="d-flex flex-column gap-2">
                            <button type="button" class="btn btn-danger btn-pill py-2 shadow-sm" @click="deleteProduct"
                                :disabled="loading">
                                <span v-if="loading" class="spinner-border spinner-border-sm me-2"></span>
                                Permanently Delete
                            </button>
                            <button type="button" class="btn btn-light btn-pill py-2 text-muted" data-bs-dismiss="modal"
                                :disabled="loading">Retain Entry</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { Modal } from 'bootstrap'

export default {
    name: 'HomeView',
    data() {
        return {
            apiUrl: 'http://localhost:5678/webhook/product',
            products: [],
            isEdit: false,
            productModal: null,
            deleteModal: null,
            loading: false,
            globalError: null,
            modalError: null,
            form: {
                pro_id: null,
                pro_name: '',
                pro_desc: '',
                pro_price: 0,
                pro_count: 0
            },
            selectedProduct: null
        }
    },
    computed: {
        totalStock() {
            return this.products.reduce((acc, p) => acc + (p.pro_count || 0), 0)
        },
        totalValue() {
            return this.products.reduce((acc, p) => acc + ((p.pro_price || 0) * (p.pro_count || 0)), 0)
        }
    },
    mounted() {
        this.productModal = new Modal(document.getElementById('productModal'))
        this.deleteModal = new Modal(document.getElementById('deleteModal'))
        this.getProducts()
    },
    methods: {
        handleError(error, target = 'global') {
            let message = 'An error occurred while connecting to system.';
            if (error.response && error.response.data && error.response.data.msg) {
                message = error.response.data.msg;
            } else if (error.message) {
                message = error.message;
            }

            if (target === 'modal') {
                this.modalError = message;
            } else {
                this.globalError = message;
            }
        },
        async getProducts() {
            this.globalError = null;
            try {
                const response = await axios.get(this.apiUrl)
                this.products = Array.isArray(response.data) ? response.data : (response.data.data || [])
            } catch (error) {
                this.handleError(error);
            }
        },
        openModal(mode, product = null) {
            this.isEdit = mode === 'edit'
            this.modalError = null
            if (this.isEdit && product) {
                this.form = { ...product }
            } else {
                this.resetForm()
            }
            this.productModal.show()
        },
        resetForm() {
            this.form = {
                pro_id: null,
                pro_name: '',
                pro_desc: '',
                pro_price: 0,
                pro_count: 0
            }
        },
        async saveProduct() {
            this.modalError = null;
            this.loading = true;
            try {
                if (this.isEdit) {
                    await axios.put(`${this.apiUrl}?id=${this.form.pro_id}`, this.form)
                } else {
                    await axios.post(this.apiUrl, this.form)
                }
                this.productModal.hide()
                this.getProducts()
            } catch (error) {
                this.handleError(error, 'modal');
            } finally {
                this.loading = false;
            }
        },
        confirmDelete(product) {
            this.modalError = null
            this.selectedProduct = product
            this.deleteModal.show()
        },
        async deleteProduct() {
            this.modalError = null;
            this.loading = true;
            try {
                await axios.delete(`${this.apiUrl}?id=${this.selectedProduct.pro_id}`)
                this.deleteModal.hide()
                this.getProducts()
            } catch (error) {
                this.handleError(error, 'modal');
            } finally {
                this.loading = false;
            }
        }
    }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;900&display=swap');

/* Base Elegant Styles */
.page-background {
    background-color: #fcfcfd;
    font-family: 'Outfit', sans-serif;
}

.tracking-widest {
    letter-spacing: 0.15em;
}

.fw-black {
    font-weight: 900;
}

/* Card Improvements */
.stat-card {
    background: white;
    border: 1px solid rgba(0, 0, 0, 0.03);
    border-radius: 20px;
    box-shadow: 0 10px 40px -10px rgba(0, 0, 0, 0.04);
}

.card-premium {
    background: white;
    border: none;
    border-radius: 24px;
    box-shadow: 0 20px 60px -20px rgba(0, 0, 0, 0.08);
}

/* Statistics Icon Circle */
.icon-circle {
    width: 60px;
    height: 60px;
    border-radius: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
}
.table-head-cell {
    background: #f8f9fa;
    color: #8898aa;
    font-size: 0.75rem;
    text-transform: uppercase;
    font-weight: 700;
    letter-spacing: 0.05em;
    padding: 1.5rem 1rem;
    border-bottom: 2px solid #f1f3f5;
}

.table-row {
    transition: all 0.2s ease;
}

.table-row:hover {
    background-color: #fcfcfd;
    transform: scale(1.002);
}

.stock-indicator {
    width: 30px;
    height: 4px;
    border-radius: 10px;
}

/* Button & UI Improvements */
.btn-pill {
    border-radius: 50px;
}

.btn-dark {
    background: #111;
    border-color: #111;
}

.btn-dark:hover {
    background: #333;
    transform: translateY(-2px);
}

.shadow-highlight {
    box-shadow: 0 8px 25px -5px rgba(0, 0, 0, 0.15);
}

.transition-up {
    transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.transition-up:hover {
    transform: translateY(-6px);
}

/* Icon Buttons */
.btn-icon {
    width: 38px;
    height: 38px;
    border-radius: 12px;
    border: none;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    transition: all 0.2s;
    background: #f8f9fa;
    color: #495057;
}

.btn-icon-warning:hover {
    background: #fff9db;
    color: #f08c00;
}

.btn-icon-danger:hover {
    background: #fff5f5;
    color: #e03131;
}

/* Modal Improvements */
.modal-content-premium {
    border-radius: 30px;
}

.modal-accent {
    border-top-left-radius: 30px;
    border-bottom-left-radius: 30px;
    background: linear-gradient(135deg, #111 0%, #333 100%);
}

.form-label-elegant {
    font-weight: 600;
    font-size: 0.85rem;
    color: #8898aa;
    text-transform: uppercase;
    margin-bottom: 0.6rem;
    display: block;
}

.form-control-elegant {
    background: #f8f9fa;
    border: 2px solid transparent;
    border-radius: 15px;
    padding: 1rem 1.2rem;
    font-weight: 500;
    transition: all 0.2s;
}

.form-control-elegant:focus {
    background: white;
    border-color: #111;
    box-shadow: 0 10px 30px -10px rgba(0, 0, 0, 0.1);
    outline: none;
}

.btn-close-custom {
    background: none;
    border: none;
    font-size: 1.2rem;
    color: #adb5bd;
    padding: 0.5rem;
    transition: color 0.2s;
}

.btn-close-custom:hover {
    color: #111;
}

/* Animations */
.fade-enter-active,
.fade-leave-active {
    transition: opacity .4s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.alert-glass {
    background: rgba(255, 255, 255, 0.7);
    backdrop-filter: blur(10px);
}

.alert-warning-soft {
    background: #fff9db;
    color: #664d03;
    border-radius: 12px;
}

.alert-danger-soft {
    background: #fff5f5;
    color: #c92a2a;
}

.delete-icon-ring {
    width: 70px;
    height: 70px;
    background: #fff5f5;
    border-radius: 100%;
    margin: 0 auto;
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>


<style scoped>
.container {
    max-width: 1100px;
}

.table-hover tbody tr:hover {
    background-color: rgba(13, 110, 253, 0.05);
}

.card {
    border-radius: 15px;
}

.btn-primary {
    background: linear-gradient(135deg, #0d6efd 0%, #0056b3 100%);
    border: none;
}

.form-control:focus {
    border-color: #0d6efd;
    box-shadow: 0 0 0 0.25rem rgba(13, 110, 253, 0.1);
}

.modal-content {
    border-radius: 20px;
}

.badge {
    font-weight: 600;
    letter-spacing: 0.3px;
}
</style>
