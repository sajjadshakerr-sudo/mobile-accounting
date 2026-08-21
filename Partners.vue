<template>
    <div class="page active relative-page">
        
        <div class="top-action-bar mb-md" v-if="!isFormOpen && partners.length > 0">
            <div class="stats-banner-card" @click="showStatsModal = true">
                <div class="d-flex align-center gap-sm">
                    <div class="sbc-icon">
                        <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="20" x2="12" y2="10"></line><line x1="18" y1="20" x2="18" y2="4"></line><line x1="6" y1="20" x2="6" y2="16"></line></svg>
                    </div>
                    <div class="sbc-text">
                        <strong class="sbc-title">آمار و گزارش حساب‌ها</strong>
                        <span class="sbc-subtitle">مشاهده کل بدهی‌ها و مطالبات</span>
                    </div>
                </div>
                <div class="sbc-arrow">
                    <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>
                </div>
            </div>
        </div>

        <button class="fab-add-btn" v-show="!isFormOpen" @click="openForm">
            <svg viewBox="0 0 24 24" width="28" height="28" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                <line x1="12" y1="5" x2="12" y2="19"></line>
                <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
        </button>

        <div v-if="isFormOpen" class="form-container-anim">
            <div class="card p-md shadow-lg" style="border: none; border-radius: 24px;">
                <h2 class="mb-md text-lg font-900 d-flex align-center gap-sm" style="color: var(--text-dark);">
                    <div style="width: 32px; height: 32px; background: var(--primary-light); color: var(--primary-dark); border-radius: 10px; display: flex; align-items: center; justify-content: center;">
                        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path><circle cx="9" cy="7" r="4"></circle></svg>
                    </div>
                    {{ editId ? 'ویرایش اطلاعات' : 'ثبت حساب جدید' }}
                </h2>
                
                <form @submit.prevent="savePartner" class="modern-form">
                    <div class="form-group mt-md">
                        <label class="form-label-elegant">دسته‌بندی حساب:</label>
                        <div class="segmented-control">
                            <label class="sc-option" :class="{'active-partner': form.contactType === 'partner'}">
                                <input type="radio" v-model="form.contactType" value="partner" style="display: none;">
                                💼 همکار
                            </label>
                            <label class="sc-option" :class="{'active-personal': form.contactType === 'personal'}">
                                <input type="radio" v-model="form.contactType" value="personal" style="display: none;">
                                👤 شخصی
                            </label>
                        </div>
                    </div>

                    <div class="elegant-section mt-md">
                        <div class="form-group">
                            <label class="form-label-elegant">نام و نام خانوادگی:</label>
                            <input type="text" v-model="form.name" class="form-control-elegant" placeholder="مثال: علی رضایی" required>
                        </div>
                        
                        <div class="form-group" v-if="form.contactType === 'partner'">
                            <label class="form-label-elegant">نام فروشگاه / مجموعه:</label>
                            <input type="text" v-model="form.storeName" class="form-control-elegant" placeholder="مثال: موبایل مرکزی">
                        </div>
                        
                        <div class="form-group mb-0">
                            <label class="form-label-elegant">شماره تماس (اختیاری):</label>
                            <input type="tel" v-model="form.phone" class="form-control-elegant dir-ltr text-center font-800" placeholder="0912...">
                        </div>
                    </div>
                    
                    <div v-if="!editId" class="elegant-section mt-md" style="background: #f8fafc; border: 1px solid #e2e8f0;">
                        <label class="form-label-elegant d-flex align-center gap-xs">
                            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><line x1="12" y1="1" x2="12" y2="23"></line><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path></svg>
                            وضعیت حساب قبلی (تراز اولیه)
                        </label>
                        <div class="segmented-control mt-sm mb-md" style="background: #e2e8f0;">
                            <label class="sc-option text-xs" :class="{'active-danger': form.balanceType === 'owe'}">
                                <input type="radio" v-model="form.balanceType" value="owe" style="display:none">
                                ما بدهکاریم
                            </label>
                            <label class="sc-option text-xs" :class="{'active-success': form.balanceType === 'demand'}">
                                <input type="radio" v-model="form.balanceType" value="demand" style="display:none">
                                ما طلبکاریم
                            </label>
                            <label class="sc-option text-xs" :class="{'active-neutral': form.balanceType === 'zero'}">
                                <input type="radio" v-model="form.balanceType" value="zero" style="display:none">
                                بی‌حساب
                            </label>
                        </div>
                        
                        <div v-if="form.balanceType !== 'zero'" class="form-group mb-0 fade-in">
                            <div class="input-with-currency-elegant">
                                <input type="text" :value="formatNumber(form.initialBalance)" @input="e => handleNumberInput(form, 'initialBalance', e)" class="form-control-elegant dir-ltr text-center font-900" style="font-size: 16px; color:var(--text-dark);" placeholder="0">
                                <span class="currency-addon-elegant">تومان</span>
                            </div>
                        </div>
                    </div>

                    <div class="d-flex gap-md mt-xl">
                        <button type="submit" class="btn btn-primary mt-0 flex-2" style="border-radius: 16px; box-shadow: 0 8px 16px rgba(37, 99, 235, 0.25);">
                            {{ editId ? 'ذخیره تغییرات' : 'ثبت حساب' }}
                        </button>
                        <button type="button" @click="closeForm" class="btn btn-secondary mt-0 flex-1" style="border-radius: 16px; background: #f1f5f9;">انصراف</button>
                    </div>
                </form>
            </div>
        </div>

        <div v-show="!isFormOpen">
            <div class="segmented-control mb-md" v-if="partners.length > 0">
                <button class="sc-option" :class="{ 'active-neutral': currentTab === 'all' }" @click="currentTab = 'all'">همه</button>
                <button class="sc-option" :class="{ 'active-partner': currentTab === 'partner' }" @click="currentTab = 'partner'">همکاران</button>
                <button class="sc-option" :class="{ 'active-personal': currentTab === 'personal' }" @click="currentTab = 'personal'">شخصی</button>
            </div>

            <div class="elegant-search-box mb-md" v-if="partners.length > 0">
                <svg class="esb-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line></svg>
                <input type="text" :value="searchInput" @input="onSearchInput" class="esb-input" placeholder="جستجوی نام شخص یا فروشگاه..." />
                <button v-if="searchInput" class="esb-clear" @click="clearSearch">×</button>
            </div>

            <div v-if="filteredPartners.length === 0" class="empty-state-elegant mt-md">
                <div class="ese-icon">📇</div>
                <p>{{ searchQuery ? 'حسابی با این مشخصات یافت نشد.' : 'لیست حساب‌ها خالی است.' }}</p>
            </div>

            <div class="contact-list" v-else>
                <div v-for="partner in filteredPartners" :key="partner.id" class="contact-card clickable" @click="openDetails(partner)">
                    <div class="cc-card-layout">
                        
                        <div class="cc-right-side">
                            <div class="cc-avatar" :class="getContactType(partner) === 'personal' ? 'avatar-personal' : 'avatar-partner'">
                                {{ partner.name.charAt(0) || 'م' }}
                            </div>
                            <div class="cc-info">
                                <strong class="cc-name">{{ partner.name }}</strong>
                                <span class="cc-sub">{{ getContactType(partner) === 'personal' ? 'حساب شخصی' : (partner.storeName || 'بدون نام فروشگاه') }}</span>
                                <span class="contact-type-badge mt-xs" :class="getContactType(partner) === 'personal' ? 'type-personal' : 'type-partner'">
                                    {{ getContactType(partner) === 'personal' ? 'شخصی' : 'همکار' }}
                                </span>
                            </div>
                        </div>

                        <div class="cc-left-side" @click.stop>
                            <div class="action-btn-group">
                                <button class="btn-edit-minimal" @click="editPartner(partner)">
                                    <svg viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path></svg>
                                </button>
                                <button class="btn-delete-minimal" @click="deletePartner(partner.id)">
                                    <svg viewBox="0 0 24 24" width="15" height="15" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path></svg>
                                </button>
                            </div>
                            
                            <div class="cc-financials mt-sm" @click="openDetails(partner)">
                                <template v-if="Number(partner.balance) === 0">
                                    <span class="sc-badge-status status-settled" style="padding: 4px 10px;">
                                        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path><polyline points="22 4 12 14.01 9 11.01"></polyline></svg>
                                        تسویه کامل
                                    </span>
                                </template>
                                <template v-else>
                                    <span class="cc-status" :class="Number(partner.balance) > 0 ? 'text-danger-dark' : 'text-success-dark'">
                                        {{ Number(partner.balance) > 0 ? 'بدهی ما:' : 'طلب ما:' }}
                                    </span>
                                    <strong class="cc-amount dir-ltr" :class="Number(partner.balance) > 0 ? 'text-danger-dark' : 'text-success-dark'">
                                        {{ formatNumber(Math.abs(partner.balance)) }}
                                    </strong>
                                    <span class="cc-currency" :class="Number(partner.balance) > 0 ? 'text-danger-dark' : 'text-success-dark'">تومان</span>
                                </template>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </div>

        <!-- مودال آمار و گزارش حساب‌ها -->
        <div v-if="showStatsModal" class="modal-overlay glass-overlay" style="z-index: 10000;" @click.self="showStatsModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>
                
                <div class="modal-top-bar-modern">
                    <div class="icon-box-modern">
                        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="20" x2="18" y2="10"></line><line x1="12" y1="20" x2="12" y2="4"></line><line x1="6" y1="20" x2="6" y2="14"></line></svg>
                    </div>
                    <div class="title-box-modern">
                        <h3 class="m-0 text-dark" style="font-size: 16px; font-weight: 900;">آمار کل حساب‌ها</h3>
                        <span class="text-muted" style="font-size: 11.5px; font-weight: 700;">مشاهده کل بدهی‌ها و مطالبات</span>
                    </div>
                    <button class="btn-circular-close-modern" @click="showStatsModal = false">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                    </button>
                </div>
                
                <div class="modern-sheet-body hide-scroll px-md pb-xl">
                    
                    <div class="stat-box-modern card-green">
                        <div class="stat-box-header">
                            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"></polyline><polyline points="17 6 23 6 23 12"></polyline></svg>
                            <span>مطالبات کل سیستم (باید بگیریم)</span>
                        </div>
                        <div class="stat-box-value">
                            <strong class="dir-ltr">{{ formatNumber(stats.totalTheyOwe) }}</strong>
                            <span class="currency">تومان</span>
                        </div>
                        <div class="white-sub-boxes mt-sm">
                            <div class="white-sub-box">
                                <span style="color:#059669;">از همکار</span>
                                <div class="d-flex align-baseline justify-center gap-xs w-100 mt-xs" style="direction: rtl;">
                                    <strong style="color:#059669; font-size: 16px;" class="dir-ltr">{{ formatNumber(stats.partnerTheyOwe) }}</strong>
                                    <span style="color:#059669; font-size: 10px; opacity: 0.85;">تومان</span>
                                </div>
                            </div>
                            <div class="white-sub-box">
                                <span style="color:#059669;">از شخصی</span>
                                <div class="d-flex align-baseline justify-center gap-xs w-100 mt-xs" style="direction: rtl;">
                                    <strong style="color:#059669; font-size: 16px;" class="dir-ltr">{{ formatNumber(stats.personalTheyOwe) }}</strong>
                                    <span style="color:#059669; font-size: 10px; opacity: 0.85;">تومان</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="stat-box-modern card-red">
                        <div class="stat-box-header">
                            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="23 18 13.5 8.5 8.5 13.5 1 6"></polyline><polyline points="17 18 23 18 23 12"></polyline></svg>
                            <span>بدهی‌های کل سیستم (باید بپردازیم)</span>
                        </div>
                        <div class="stat-box-value">
                            <strong class="dir-ltr">{{ formatNumber(stats.totalWeOwe) }}</strong>
                            <span class="currency">تومان</span>
                        </div>
                        <div class="white-sub-boxes mt-sm">
                            <div class="white-sub-box">
                                <span style="color:#dc2626;">به همکار</span>
                                <div class="d-flex align-baseline justify-center gap-xs w-100 mt-xs" style="direction: rtl;">
                                    <strong style="color:#dc2626; font-size: 16px;" class="dir-ltr">{{ formatNumber(stats.partnerWeOwe) }}</strong>
                                    <span style="color:#dc2626; font-size: 10px; opacity: 0.85;">تومان</span>
                                </div>
                            </div>
                            <div class="white-sub-box">
                                <span style="color:#dc2626;">به شخصی</span>
                                <div class="d-flex align-baseline justify-center gap-xs w-100 mt-xs" style="direction: rtl;">
                                    <strong style="color:#dc2626; font-size: 16px;" class="dir-ltr">{{ formatNumber(stats.personalWeOwe) }}</strong>
                                    <span style="color:#dc2626; font-size: 10px; opacity: 0.85;">تومان</span>
                                </div>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </div>

        <div v-if="showDetailsModal" class="modal-overlay glass-overlay" @click.self="showDetailsModal = false">
            <div class="bottom-sheet-container details-sheet">
                <div class="sheet-drag-handle"></div>

                <div class="custom-scroll sheet-body-padded">
                    
                    <div class="modal-top-bar mb-xl">
                        <div class="d-flex align-center gap-md">
                            <div class="cc-avatar-lg" :class="getContactType(activePartner) === 'personal' ? 'avatar-personal' : 'avatar-partner'">
                                {{ activePartner?.name?.charAt(0) || 'م' }}
                            </div>
                            <div class="profile-info d-flex flex-column gap-xs text-right">
                                <h3 class="profile-name m-0" style="font-size: 17px; font-weight: 900;">{{ activePartner?.name }}</h3>
                                <span class="text-muted dir-ltr" style="font-size: 12px; font-weight: 700;">{{ activePartner?.phone || 'بدون شماره' }}</span>
                            </div>
                        </div>
                        <button class="btn-circular-close bg-light" @click="showDetailsModal = false">
                            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                        </button>
                    </div>

                    <div class="fintech-card mb-xl" :class="Number(activePartner?.balance) > 0 ? 'fc-danger' : (Number(activePartner?.balance) < 0 ? 'fc-success' : 'fc-neutral')">
                        <div class="fc-glow"></div>
                        <div class="fc-top">
                            <span class="fc-label">وضعیت حساب فعلی</span>
                            <div class="fc-icon">
                                <svg v-if="Number(activePartner?.balance) !== 0" viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 12h-4l-3 9L9 3l-3 9H2"></path></svg>
                                <svg v-else viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2"><polyline points="20 6 9 17 4 12"></polyline></svg>
                            </div>
                        </div>
                        <div class="fc-middle">
                            <strong class="fc-amount dir-ltr">{{ formatNumber(Math.abs(activePartner?.balance)) }}</strong>
                            <span class="fc-currency">تومان</span>
                        </div>
                        <div class="fc-bottom">
                            <span class="fc-status-text">
                                {{ Number(activePartner?.balance) > 0 ? 'شما باید این مبلغ را بپردازید' : (Number(activePartner?.balance) < 0 ? 'این شخص باید مبلغ را بپردازد' : 'حساب کاملاً تسویه است') }}
                            </span>
                        </div>
                    </div>

                    <button @click="openTxModal" class="btn-add-tx mb-xl">
                        <svg viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>
                        ثبت پرداختی یا دریافتی جدید
                    </button>

                    <div class="payments-timeline-wrapper">
                        <div class="timeline-header mb-lg">
                            <div class="d-flex align-center gap-sm">
                                <div class="th-icon">
                                    <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line></svg>
                                </div>
                                <h4 class="th-title m-0">ریز مراودات مالی</h4>
                            </div>
                            <div class="th-badge">
                                {{ activePartner?.transactions?.length || 0 }} مورد
                            </div>
                        </div>

                        <div v-if="!activePartner?.transactions || activePartner.transactions.length === 0" class="empty-state-elegant py-md">
                            <p>هیچ تراکنشی ثبت نشده است.</p>
                        </div>

                        <div v-else class="timeline-list">
                            <div v-for="tx in [...activePartner.transactions].reverse()" :key="tx.id" class="tl-item">
                                <div class="tl-marker" :class="tx.type === 'plus' ? 'marker-danger' : 'marker-success'">
                                    <svg v-if="tx.type === 'plus'" viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 17 12 9 4 17"></polyline></svg>
                                    <svg v-else viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 9 12 17 4 9"></polyline></svg>
                                </div>
                                
                                <div class="tl-content-elegant">
                                    <div class="tl-top-row">
                                        <div class="tl-amount-wrapper">
                                            <strong class="dir-ltr m-0" style="font-size: 17px; font-weight: 900;" :class="tx.type === 'plus' ? 'text-danger-dark' : 'text-success-dark'">
                                                {{ formatNumber(tx.amount) }}
                                            </strong>
                                        </div>
                                        <div class="tl-date-wrapper">
                                            <span class="dir-ltr text-muted" style="font-size: 12px; font-weight: 800;">{{ tx.date }}</span>
                                            <button class="tl-del-elegant" @click="deleteTx(tx.id)" title="حذف">
                                                <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                                            </button>
                                        </div>
                                    </div>
                                    <div v-if="tx.description" class="tx-desc-elegant text-right" dir="rtl">
                                        بابت: {{ tx.description }}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div v-if="showTxModal" class="modal-overlay glass-overlay" style="z-index: 10005;" @click.self="showTxModal = false">
            <div class="bottom-sheet-container">
                <div class="sheet-drag-handle"></div>
                
                <div class="custom-scroll sheet-body-padded w-100">
                    <div class="modal-top-bar mb-lg">
                        <h3 class="text-md font-900 m-0 text-dark">ثبت مبادله مالی</h3>
                        <button class="btn-circular-close bg-light" @click="showTxModal = false">
                            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                        </button>
                    </div>
                    
                    <div class="tx-segmented-control mb-lg">
                        <label class="tx-sc-option" :class="{'active-danger': txForm.type === 'plus'}">
                            <input type="radio" v-model="txForm.type" value="plus" style="display:none"> 
                            <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 17 12 9 4 17"></polyline></svg>
                            <span v-if="getContactType(activePartner) === 'partner'">خرید قطعات / بدهکار شدن</span>
                            <span v-else>پرداخت وجه</span>
                        </label>
                        <label class="tx-sc-option" :class="{'active-success': txForm.type === 'minus'}">
                            <input type="radio" v-model="txForm.type" value="minus" style="display:none"> 
                            <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5"><polyline points="20 9 12 17 4 9"></polyline></svg>
                            <span v-if="getContactType(activePartner) === 'partner'">واریز وجه / بستانکار شدن</span>
                            <span v-else>دریافت وجه</span>
                        </label>
                    </div>
                    
                    <div class="tx-amount-group mb-lg">
                        <label class="form-label-elegant text-center mb-sm d-block">مبلغ</label>
                        <div class="tx-amount-input-box">
                            <div class="input-with-currency-elegant" style="background: transparent; border: none; padding: 0;">
                                <input type="text" :value="formatNumber(txForm.amount)" @input="e => handleNumberInput(txForm, 'amount', e)" class="tx-huge-input dir-ltr" placeholder="0" required>
                                <span class="currency-addon-elegant" style="left: auto; right: 0; position: relative; font-size: 14px; margin-right: 8px;">تومان</span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="form-group mb-md">
                        <label class="form-label-elegant">بابت / توضیحات (اختیاری):</label>
                        <input type="text" v-model="txForm.description" class="form-control-elegant" placeholder="مثال: بابت قطعات یا تسویه">
                    </div>
                    
                    <div class="form-group mb-xl">
                        <label class="form-label-elegant">تاریخ تراکنش:</label>
                        <input type="text" v-model="txForm.date" class="form-control-elegant dir-ltr text-center font-800">
                    </div>
                    
                    <button @click="saveTx" class="btn-tx-submit">
                        ثبت تراکنش
                    </button>
                </div>
            </div>
        </div>

    </div>
</template>


<script>
const { ref, reactive, computed, onMounted, onActivated } = Vue;

export default {
    setup() {
        const { formatNumber, unformatNumber, getPersianDate, toEnglishDigits } = window.SysUtils;

        const partners = ref([]);
        const isFormOpen = ref(false);
        const editId = ref(null);
        
        const currentTab = ref('all');
        const searchInput = ref('');
        const searchQuery = ref('');
        let debounceTimer;

        const showDetailsModal = ref(false);
        const showTxModal = ref(false);
        const showStatsModal = ref(false); 
        const activePartner = ref(null);

        const form = reactive({
            contactType: 'partner', name: '', storeName: '', phone: '', initialBalance: '', balanceType: 'zero'
        });

        const txForm = reactive({ amount: '', date: '', type: 'plus', description: '' });

        const handleNumberInput = (targetObj, field, e) => {
            const el = e.target;
            const cursorPos = el.selectionStart;
            const oldLen = el.value.length;
            
            targetObj[field] = unformatNumber(el.value);
            
            Vue.nextTick(() => {
                const newLen = el.value.length;
                const newPos = Math.max(0, cursorPos + (newLen - oldLen));
                el.setSelectionRange(newPos, newPos);
            });
        };

        const onSearchInput = (e) => {
            searchInput.value = e.target.value;
            clearTimeout(debounceTimer);
            debounceTimer = setTimeout(() => {
                searchQuery.value = searchInput.value;
            }, 300);
        };

        const clearSearch = () => {
            searchInput.value = '';
            searchQuery.value = '';
        };

        const loadData = async () => {
            if (window.DB) {
                partners.value = await window.DB.getAll('partners');
                partners.value.sort((a, b) => b.timestamp - a.timestamp);
                
                if (activePartner.value) {
                    const updatedPartner = partners.value.find(p => p.id === activePartner.value.id);
                    if (updatedPartner) activePartner.value = updatedPartner;
                }
            }
        };

        const getContactType = (p) => {
            if (!p) return 'partner';
            return p.contactType || 'partner'; 
        };

        const filteredPartners = computed(() => {
            let result = partners.value;
            
            if (currentTab.value !== 'all') {
                result = result.filter(p => getContactType(p) === currentTab.value);
            }

            if (searchQuery.value) {
                const q = searchQuery.value.toLowerCase();
                result = result.filter(p => 
                    (p.name && p.name.toLowerCase().includes(q)) ||
                    (p.storeName && p.storeName.toLowerCase().includes(q))
                );
            }
            
            return result;
        });

        const stats = computed(() => {
            let partnerWeOwe = 0;
            let partnerTheyOwe = 0;
            let personalWeOwe = 0;
            let personalTheyOwe = 0;

            partners.value.forEach(p => {
                const bal = Number(p.balance || 0);
                const type = getContactType(p);
                
                if (bal > 0) {
                    if (type === 'partner') partnerWeOwe += bal;
                    else personalWeOwe += bal;
                } else if (bal < 0) {
                    if (type === 'partner') partnerTheyOwe += Math.abs(bal);
                    else personalTheyOwe += Math.abs(bal);
                }
            });
            return { 
                totalWeOwe: partnerWeOwe + personalWeOwe, 
                totalTheyOwe: partnerTheyOwe + personalTheyOwe,
                partnerWeOwe,
                partnerTheyOwe,
                personalWeOwe,
                personalTheyOwe
            };
        });

        const calculatePartnerBalance = (partner) => {
            let bal = Number(partner.initialBalance || 0);
            if (partner.transactions && Array.isArray(partner.transactions)) {
                partner.transactions.forEach(tx => {
                    if (tx.type === 'plus') bal += Number(tx.amount || 0);
                    else if (tx.type === 'minus') bal -= Number(tx.amount || 0);
                });
            }
            partner.balance = bal;
            return bal;
        };

        const openForm = () => {
            Object.assign(form, { contactType: 'partner', name: '', storeName: '', phone: '', initialBalance: '', balanceType: 'zero' });
            editId.value = null;
            isFormOpen.value = true;
        };

        const closeForm = () => { isFormOpen.value = false; };

        const savePartner = async () => {
            try {
                const existing = editId.value ? partners.value.find(p => p.id === editId.value) : null;
                
                const baseData = existing ? JSON.parse(JSON.stringify(existing)) : {
                    id: window.DB.generateId(),
                    timestamp: Date.now(),
                    transactions: []
                };

                baseData.contactType = form.contactType;
                baseData.name = form.name;
                baseData.storeName = form.contactType === 'personal' ? '' : form.storeName;
                baseData.phone = toEnglishDigits(form.phone);
                
                let initBal = 0;
                if (form.balanceType === 'owe') initBal = Number(form.initialBalance) || 0;
                else if (form.balanceType === 'demand') initBal = -(Number(form.initialBalance) || 0);
                
                baseData.initialBalance = initBal;
                
                calculatePartnerBalance(baseData);

                await window.DB.put('partners', baseData);
                closeForm();
                await loadData();
            } catch (err) {
                alert('خطا در ذخیره اطلاعات!');
            }
        };

        const deletePartner = async (id) => {
            if (confirm("هشدار: با حذف این شخص، تمامی تاریخچه تراکنش‌ها و حساب‌های ایشان از سیستم پاک می‌شود. آیا مطمئن هستید؟")) {
                await window.DB.delete('partners', id);
                showDetailsModal.value = false;
                await loadData();
            }
        };

        const editPartner = (partner) => {
            showDetailsModal.value = false;
            editId.value = partner.id;
            
            let balType = 'zero';
            let initBal = partner.initialBalance || 0;
            if (initBal > 0) balType = 'owe';
            else if (initBal < 0) { balType = 'demand'; initBal = Math.abs(initBal); }
            
            Object.assign(form, {
                contactType: getContactType(partner),
                name: partner.name, storeName: partner.storeName, phone: partner.phone, 
                initialBalance: initBal, balanceType: balType
            });
            isFormOpen.value = true;
        };

        const openDetails = (partner) => {
            activePartner.value = partner;
            showDetailsModal.value = true;
        };

        const openTxModal = () => {
            txForm.amount = '';
            txForm.description = ''; 
            txForm.date = getPersianDate();
            txForm.type = 'plus';
            showTxModal.value = true;
        };

        const saveTx = async () => {
            if (!activePartner.value) return;
            const amt = Number(txForm.amount);
            if (amt <= 0 || isNaN(amt)) return alert('مبلغ نامعتبر است.');
            
            const partner = JSON.parse(JSON.stringify(activePartner.value));
            if (!partner.transactions) partner.transactions = [];
            
            partner.transactions.push({ 
                id: Date.now(), 
                amount: amt, 
                type: txForm.type,
                date: toEnglishDigits(txForm.date),
                description: txForm.description || ''
            });
            
            calculatePartnerBalance(partner);
            
            await window.DB.put('partners', partner);
            
            showTxModal.value = false;
            
            await loadData();
        };

        const deleteTx = async (txId) => {
            if (!confirm("آیا از حذف این تراکنش اطمینان دارید؟")) return;
            
            const partner = JSON.parse(JSON.stringify(activePartner.value));
            
            partner.transactions = partner.transactions.filter(t => t.id !== txId);
            calculatePartnerBalance(partner);
            
            await window.DB.put('partners', partner);
            await loadData();
        };

        onMounted(() => { loadData(); });
        onActivated(() => { loadData(); });

        return {
            partners, isFormOpen, editId, form, currentTab, searchInput, searchQuery, onSearchInput, clearSearch, filteredPartners, stats,
            showDetailsModal, showTxModal, showStatsModal, txForm, activePartner,
            getContactType, formatNumber, unformatNumber, openForm, closeForm, savePartner, deletePartner, 
            editPartner, openDetails, openTxModal, saveTx, deleteTx, handleNumberInput
        };
    }
}
</script>


<style scoped>
.form-container-anim { animation: slideDown 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); }
@keyframes slideDown { from { opacity: 0; transform: translateY(-15px) scale(0.98); } to { opacity: 1; transform: translateY(0) scale(1); } }
.fade-in { animation: fadeIn 0.3s ease; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
.relative-page { padding-bottom: 120px; }
.w-100 { width: 100%; }
.mb-xs { margin-bottom: 8px; }
.mb-sm { margin-bottom: 12px; }
.mb-md { margin-bottom: 16px; }
.mb-lg { margin-bottom: 24px; }
.mb-xl { margin-bottom: 32px; }
.mt-sm { margin-top: 10px; }
.mt-xs { margin-top: 4px; }
.px-md { padding-left: 16px; padding-right: 16px; }
.pb-xl { padding-bottom: 32px; }
.py-xs { padding-top: 2px; padding-bottom: 2px; }
.gap-xs { gap: 6px; }
.gap-sm { gap: 12px; }
.gap-md { gap: 16px; }
.text-left { text-align: left; }
.text-right { text-align: right; }
.m-0 { margin: 0; }
.text-danger-dark { color: #b91c1c !important; }
.text-success-dark { color: #047857 !important; }
.text-muted { color: #64748b; }
.text-dark { color: #0f172a; }

.top-action-bar { display: flex; gap: 8px; width: 100%; align-items: stretch; margin-bottom: 16px; }
.stats-banner-card { flex: 1; background: linear-gradient(135deg, var(--primary), var(--primary-dark)); border-radius: 16px; padding: 14px 16px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; color: white; box-shadow: 0 6px 15px rgba(37, 99, 235, 0.25); transition: all 0.3s ease; }
.stats-banner-card:active { transform: scale(0.97); }
.sbc-icon { background: rgba(255,255,255,0.2); width: 38px; height: 38px; border-radius: 12px; display: flex; align-items: center; justify-content: center; }
.sbc-text { display: flex; flex-direction: column; }
.sbc-title { font-size: 14px; font-weight: 900; }
.sbc-subtitle { font-size: 10px; opacity: 0.9; }
.sbc-arrow { background: rgba(255,255,255,0.15); width: 28px; height: 28px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }

.fab-add-btn { position: fixed; bottom: 110px; left: 20px; width: 56px; height: 56px; background: linear-gradient(135deg, var(--primary), var(--primary-dark)); color: white; border: none; border-radius: 20px; display: flex; align-items: center; justify-content: center; box-shadow: 0 8px 20px rgba(37, 99, 235, 0.35); cursor: pointer; z-index: 90; transition: transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1); }
.fab-add-btn:active { transform: scale(0.9); box-shadow: 0 4px 10px rgba(37, 99, 235, 0.2); }

.elegant-section { border-radius: 16px; padding: 14px; margin-bottom: 16px; }
.form-label-elegant { display: block; font-size: 11.5px; font-weight: 800; color: #64748b; margin-bottom: 6px; }
.form-control-elegant { width: 100%; min-height: 48px; padding: 10px 16px; background: #f8fafc; border: 1px solid #cbd5e1; border-radius: 14px; font-size: 14px; font-weight: 700; color: #0f172a; transition: all 0.3s ease; outline: none; }
.form-control-elegant:focus { background: #ffffff; border-color: var(--primary); box-shadow: 0 0 0 3px var(--primary-light); }

/* مینیمال کردن فیلدهای پولی */
.input-with-currency-elegant { position: relative; display: flex; align-items: center; width: 100%; }
.input-with-currency-elegant .form-control-elegant { padding-left: 50px; }
.currency-addon-elegant { position: absolute; left: 14px; font-size: 11px; font-weight: 800; color: #94a3b8; pointer-events: none; }

.segmented-control { display: flex; background: #f1f5f9; padding: 4px; border-radius: 14px; box-shadow: inset 0 2px 4px rgba(0,0,0,0.02); }
.sc-option { flex: 1; display: flex; align-items: center; justify-content: center; padding: 10px 4px; border-radius: 10px; font-size: 12px; font-weight: 800; color: #64748b; cursor: pointer; transition: all 0.3s ease; border: none; background: transparent; white-space: nowrap; }
.sc-option.active-neutral { background: white; color: #0f172a; box-shadow: 0 2px 6px rgba(0,0,0,0.06); }
.sc-option.active-partner { background: white; color: #6d28d9; box-shadow: 0 2px 6px rgba(109, 40, 217, 0.15); border: 1px solid #ddd6fe; }
.sc-option.active-personal { background: white; color: #059669; box-shadow: 0 2px 6px rgba(16, 185, 129, 0.15); border: 1px solid #a7f3d0; }
.sc-option.active-danger { background: white; color: #dc2626; box-shadow: 0 2px 6px rgba(220, 38, 38, 0.15); border: 1px solid #fca5a5; }
.sc-option.active-success { background: white; color: #059669; box-shadow: 0 2px 6px rgba(16, 185, 129, 0.15); border: 1px solid #a7f3d0; }

.elegant-search-box { position: relative; display: flex; align-items: center; background: #ffffff; border-radius: 16px; box-shadow: 0 2px 10px rgba(0,0,0,0.03); border: 1px solid #e2e8f0; }
.esb-icon { position: absolute; right: 14px; width: 18px; color: #94a3b8; }
.esb-input { width: 100%; border: none; background: transparent; padding: 14px 44px 14px 14px; font-size: 13px; font-weight: 700; outline: none; color: #0f172a; }
.esb-clear { position: absolute; left: 10px; background: #f1f5f9; color: #64748b; border: none; border-radius: 50%; width: 22px; height: 22px; display: flex; align-items: center; justify-content: center; cursor: pointer; }

.contact-list { display: flex; flex-direction: column; gap: 12px; }
.contact-card { background: #ffffff; border-radius: 20px; padding: 16px 14px; border: 1px solid #f1f5f9; box-shadow: 0 2px 10px rgba(15, 23, 42, 0.03); transition: transform 0.2s ease, box-shadow 0.2s ease; cursor: pointer; }
.contact-card:active { transform: scale(0.98); box-shadow: 0 0 0 transparent; border-color: var(--primary-light); }
.cc-card-layout { display: flex; justify-content: space-between; align-items: flex-start; gap: 10px; width: 100%; }
.cc-right-side { display: flex; gap: 12px; align-items: flex-start; flex: 1; min-width: 0; }
.cc-avatar { width: 48px; height: 48px; border-radius: 16px; display: flex; align-items: center; justify-content: center; font-size: 20px; font-weight: 900; color: white; flex-shrink: 0; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
.avatar-partner { background: linear-gradient(135deg, #a78bfa, #818cf8); }
.avatar-personal { background: linear-gradient(135deg, #34d399, #10b981); }
.cc-info { display: flex; flex-direction: column; gap: 2px; flex: 1; min-width: 0; align-items: flex-start; }
.cc-name { font-size: 15px; font-weight: 900; color: #0f172a; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; width: 100%; text-align: right; line-height: 1.4; }
.cc-sub { font-size: 11px; font-weight: 600; color: #64748b; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; width: 100%; text-align: right; }
.contact-type-badge { font-size: 10px; font-weight: 800; padding: 4px 8px; border-radius: 8px; white-space: nowrap; display: inline-block; }
.type-personal { background: #d1fae5; color: #047857; }
.type-partner { background: #ede9fe; color: #6d28d9; }
.cc-left-side { display: flex; flex-direction: column; align-items: flex-end; justify-content: space-between; flex-shrink: 0; min-width: max-content; }
.action-btn-group { display: flex; gap: 8px; margin-bottom: 12px; }
.btn-edit-minimal, .btn-delete-minimal { width: 32px; height: 32px; border-radius: 10px; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; border: none; }
.btn-edit-minimal { background: #eff6ff; color: #3b82f6; }
.btn-edit-minimal:active { background: #bfdbfe; transform: scale(0.9); }
.btn-delete-minimal { background: #fef2f2; color: #ef4444; }
.btn-delete-minimal:active { background: #fecaca; transform: scale(0.9); }
.cc-financials { display: flex; align-items: center; justify-content: flex-end; gap: 4px; cursor: pointer; flex-wrap: wrap; }
.cc-status { font-size: 11.5px; font-weight: 800; }
.cc-amount { font-size: 15.5px; font-weight: 900; letter-spacing: -0.5px; }
.cc-currency { font-size: 11px; font-weight: 700; opacity: 0.75; margin-right: 0;}
.empty-state-elegant { display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px 20px; background: #f8fafc; border-radius: 20px; border: 1px dashed #cbd5e1; text-align: center; }

/* آیکون وضعیت تسویه در لیست حساب‌ها */
.sc-badge-status { font-size: 10.5px; font-weight: 800; padding: 4px 10px; border-radius: 8px; display: inline-flex; align-items: center; gap: 4px; text-align: center; }
.status-settled { background: #d1fae5; color: #047857; border: 1px solid #a7f3d0; }

.stat-box-modern { border-radius: 20px; padding: 16px; display: flex; flex-direction: column; align-items: center; margin-bottom: 14px; direction: rtl; text-align: center; }
.stat-box-header { display: flex; align-items: center; justify-content: center; gap: 8px; font-size: 13.5px; font-weight: 800; margin-bottom: 12px; width: 100%; text-align: center; }
.stat-box-header svg { width: 18px; height: 18px; flex-shrink: 0; }
.stat-box-value { display: flex; align-items: center; justify-content: center; gap: 4px; margin-bottom: 16px; direction: rtl; width: 100%; }
.stat-box-value strong { font-size: 30px; font-weight: 900; letter-spacing: -0.5px; line-height: 1; margin: 0; padding: 0; }
.stat-box-value .currency { font-size: 13px; font-weight: 800; opacity: 0.9; margin: 0; padding: 0; }
.white-sub-boxes { display: flex; gap: 10px; width: 100%; justify-content: center; flex-wrap: wrap; }
.white-sub-box { background: #ffffff; border-radius: 14px; flex: 1; min-width: 45%; padding: 12px; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center; gap: 6px; box-shadow: 0 2px 6px rgba(0,0,0,0.02); }
.white-sub-box span { font-size: 11.5px; font-weight: 800; opacity: 0.9; text-align: center; }
.white-sub-box strong { font-size: 16px; font-weight: 900; line-height: 1; }
.card-green { background: #f0fdf4; border: 1px solid #bbf7d0; color: #059669; }
.card-red { background: #fef2f2; border: 1px solid #fecaca; color: #dc2626; }

.modal-top-bar-modern { display: flex; align-items: center; justify-content: space-between; width: 100%; flex-shrink: 0; margin-bottom: 24px; padding: 0 8px; direction: rtl; }
.icon-box-modern { width: 48px; height: 48px; border-radius: 16px; background: #eff6ff; color: #2563eb; display: flex; align-items: center; justify-content: center; flex-shrink: 0; margin-left: 14px; }
.title-box-modern { text-align: right; flex: 1; margin-right: 14px; }
.btn-circular-close-modern { width: 40px; height: 40px; border-radius: 50%; background: #f1f5f9; color: #475569; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; }
.btn-circular-close-modern:active { transform: scale(0.9); background: #e2e8f0; }

.glass-overlay { align-items: flex-end; padding: 0; background: rgba(17, 24, 39, 0.45); backdrop-filter: blur(6px); display: flex; justify-content: center; z-index: 10000; position: fixed !important; inset: 0 !important; }
.modern-bottom-sheet { background: #ffffff; width: 100%; max-width: 600px; border-radius: 36px 36px 0 0; padding: 20px 24px 30px 24px; animation: slideUpModern 0.4s cubic-bezier(0.2, 0.8, 0.2, 1); display: flex; flex-direction: column; max-height: 92vh; box-shadow: 0 -10px 40px rgba(0,0,0,0.1); position: relative; }
@keyframes slideUpModern { from { transform: translateY(100%); } to { transform: translateY(0); } }
.sheet-drag-handle { width: 48px; height: 5px; background: #e5e7eb; border-radius: 4px; margin: 0 auto 20px auto; flex-shrink: 0; }
.modern-sheet-body { overflow-y: auto; padding-bottom: 20px; }
.hide-scroll::-webkit-scrollbar { display: none; }

.bottom-sheet-container { background: #ffffff; width: 100%; max-width: 600px; border-radius: 32px 32px 0 0; box-shadow: 0 -4px 24px rgba(0,0,0,0.1); position: relative; animation: slideUp 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); display: flex; flex-direction: column; max-height: 92vh; overflow: hidden; }
@keyframes slideUp { from { transform: translateY(100%); } to { transform: translateY(0); } }
.custom-scroll { overflow-y: auto; flex: 1; padding-bottom: 120px !important; }
.custom-scroll::-webkit-scrollbar { display: none; }
.sheet-body-padded { padding: 0 20px; }
.modal-top-bar { display: flex; justify-content: space-between; align-items: center; width: 100%; flex-shrink: 0; margin-bottom: 20px; }
.btn-circular-close { width: 36px; height: 36px; border-radius: 50%; background: #f3f4f6; border: none; color: #4b5563; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; }
.btn-circular-close:active { transform: scale(0.9); background: #e2e8f0; }
.cc-avatar-lg { width: 60px; height: 60px; border-radius: 18px; display: flex; align-items: center; justify-content: center; font-size: 26px; font-weight: 900; color: white; flex-shrink: 0; box-shadow: 0 6px 15px rgba(0,0,0,0.15); }
.bg-light { background: #f1f5f9; color: #475569; }

.btn-add-tx { width: 100%; display: flex; align-items: center; justify-content: center; gap: 10px; background: #0f172a; color: white; border-radius: 20px; min-height: 60px; font-size: 16px; font-weight: 900; border: none; cursor: pointer; box-shadow: 0 8px 16px rgba(15, 23, 42, 0.15); transition: transform 0.2s ease; flex-shrink: 0; }
.btn-add-tx:active { transform: scale(0.97); }

.fintech-card { position: relative; border-radius: 24px; padding: 24px; color: white; overflow: hidden; box-shadow: 0 10px 25px rgba(0,0,0,0.1); display: flex; flex-direction: column; gap: 16px; width: 100%; flex-shrink: 0; }
.fc-danger { background: linear-gradient(135deg, #e11d48, #be123c); }
.fc-success { background: linear-gradient(135deg, #059669, #047857); }
.fc-neutral { background: linear-gradient(135deg, #475569, #334155); }
.fc-glow { position: absolute; top: -50px; right: -50px; width: 150px; height: 150px; background: radial-gradient(circle, rgba(255,255,255,0.15) 0%, transparent 70%); border-radius: 50%; pointer-events: none; }
.fc-top { display: flex; justify-content: space-between; align-items: center; position: relative; z-index: 1; }
.fc-label { font-size: 13px; font-weight: 800; opacity: 0.9; }
.fc-icon { width: 36px; height: 36px; background: rgba(255,255,255,0.2); border-radius: 50%; display: flex; align-items: center; justify-content: center; }
.fc-middle { display: flex; align-items: center; gap: 4px; position: relative; z-index: 1; direction: rtl; }
.fc-amount { font-size: 38px; font-weight: 900; letter-spacing: -1px; line-height: 1; }
.fc-currency { font-size: 14px; font-weight: 700; opacity: 0.8; }
.fc-bottom { position: relative; z-index: 1; padding-top: 14px; border-top: 1px solid rgba(255,255,255,0.2); }
.fc-status-text { font-size: 12px; font-weight: 800; opacity: 0.95; }

.timeline-header { display: flex; justify-content: space-between; align-items: center; }
.th-icon { width: 38px; height: 38px; border-radius: 12px; background: #f1f5f9; color: #475569; display: flex; align-items: center; justify-content: center; }
.th-title { font-size: 16px; font-weight: 900; color: #0f172a; }
.th-badge { padding: 6px 12px; border-radius: 12px; background: #e2e8f0; color: #334155; font-size: 12px; font-weight: 800; }
.timeline-list { display: flex; flex-direction: column; gap: 16px; padding-right: 18px; position: relative; }
.timeline-list::before { content: ''; position: absolute; right: 33px; top: 12px; bottom: 12px; width: 2px; background: #e2e8f0; z-index: 0; }
.tl-item { display: flex; gap: 16px; position: relative; z-index: 1; align-items: stretch; }
.tl-marker { width: 32px; height: 32px; border-radius: 50%; display: flex; align-items: center; justify-content: center; flex-shrink: 0; border: 4px solid #ffffff; z-index: 2; margin-top: 14px; }
.marker-danger { background: #fee2e2; color: #dc2626; box-shadow: 0 2px 6px rgba(220, 38, 38, 0.15); }
.marker-success { background: #d1fae5; color: #059669; box-shadow: 0 2px 6px rgba(5, 150, 105, 0.15); }
.tl-content-elegant { flex: 1; background: #ffffff; border: 1px solid #f1f5f9; border-radius: 16px; padding: 16px; box-shadow: 0 4px 16px rgba(0,0,0,0.02); display: flex; flex-direction: column; justify-content: center; }
.tl-top-row { display: flex; justify-content: space-between; align-items: center; width: 100%; }
.tl-amount-wrapper { display: flex; align-items: center; gap: 4px; }
.tl-date-wrapper { display: flex; align-items: center; gap: 12px; }
.tl-del-elegant { background: #f8fafc; color: #94a3b8; border: none; border-radius: 10px; width: 32px; height: 32px; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; }
.tl-del-elegant:active { background: #fee2e2; color: #dc2626; transform: scale(0.9); }
.tx-desc-elegant { font-size: 12px; font-weight: 600; color: #64748b; padding-top: 12px; border-top: 1px dashed #e2e8f0; margin-top: 12px; line-height: 1.6; }
.tx-segmented-control { display: flex; background: #f1f5f9; padding: 6px; border-radius: 16px; gap: 6px; flex-shrink: 0; }
.tx-sc-option { flex: 1; display: flex; align-items: center; justify-content: center; gap: 6px; padding: 12px 4px; border-radius: 12px; font-size: 12.5px; font-weight: 800; color: #64748b; cursor: pointer; transition: 0.3s ease; border: 1px solid transparent; white-space: nowrap; }
.tx-sc-option.active-danger { background: #ffffff; color: #dc2626; border-color: #fca5a5; box-shadow: 0 4px 12px rgba(220, 38, 38, 0.1); }
.tx-sc-option.active-success { background: #ffffff; color: #059669; border-color: #a7f3d0; box-shadow: 0 4px 12px rgba(16, 185, 129, 0.1); }
.tx-amount-group { background: #f8fafc; border: 1px solid #e2e8f0; border-radius: 20px; padding: 16px; text-align: center; flex-shrink: 0; }
.tx-huge-input { width: 100%; border: none; background: transparent; font-size: 32px; font-weight: 900; color: #0f172a; outline: none; text-align: center; }
.tx-huge-input::placeholder { color: #cbd5e1; }
.btn-tx-submit { width: 100%; background: linear-gradient(135deg, var(--primary), var(--primary-dark)); color: white; border: none; border-radius: 18px; min-height: 56px; font-size: 16px; font-weight: 900; box-shadow: 0 8px 20px rgba(37, 99, 235, 0.25); cursor: pointer; transition: transform 0.2s ease; flex-shrink: 0; }
.btn-tx-submit:active { transform: scale(0.97); }
</style>

