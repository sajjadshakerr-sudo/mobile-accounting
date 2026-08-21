<template>
    <div class="page active relative-page">
        
        <div v-show="!isFormOpen" class="top-action-bar mb-md">
            <div class="stats-banner-card" style="background: linear-gradient(135deg, #0ea5e9, #0f766e);" @click="showStatsModal = true">
                <div class="d-flex align-center gap-sm">
                    <div class="sbc-icon">
                        <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path></svg>
                    </div>
                    <div class="sbc-text">
                        <strong class="sbc-title">گزارش مالی تعمیرات</strong>
                        <span class="sbc-subtitle">مشاهده اجرت‌ها و مطالبات</span>
                    </div>
                </div>
                <div class="sbc-arrow">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>
                </div>
            </div>
            
            <div class="compact-filter-btn" @click="showFilterModal = true" title="تغییر بازه زمانی">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-warning-dark"><polygon points="22 3 2 3 10 12.46 10 19 14 21 14 12.46 22 3"></polygon></svg>
                <span class="cf-label">{{ filterLabel }}</span>
            </div>
        </div>

        <div v-show="!isFormOpen" class="app-search-wrapper mb-md">
            <div class="app-search-inner">
                <svg class="app-search-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line></svg>
                <input type="text" :value="searchInput" @input="onSearchInput" class="app-search-input" placeholder="جستجوی مشتری، مدل دستگاه یا مشکل..." />
                <button v-if="searchInput" class="app-search-clear" @click="clearSearch">
                    <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                </button>
            </div>
        </div>

        <!-- فرم ثبت تعمیرات -->
        <div v-if="isFormOpen" class="form-container-anim">
            <div class="card p-md" style="padding-bottom: 24px;">
                <h2 class="mb-md text-lg font-900" style="color: var(--text-dark);">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="vertical-align: middle; margin-left: 6px; color: #0d9488;"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path></svg>
                    {{ editId ? 'ویرایش پرونده' : 'پذیرش دستگاه جدید' }}
                </h2>
                <form @submit.prevent="saveRepair" class="compact-form modern-form">
                    
                    <div class="modern-section">
                        <div class="ms-header" style="color: #0f766e;">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg>
                            <span>اطلاعات پذیرش</span>
                        </div>
                        <div class="form-row">
                            <div class="form-group"><label>تاریخ:</label><input type="text" v-model="form.date" class="form-control text-center" required dir="ltr"></div>
                            <div class="form-group"><label>مدل دستگاه (اجباری):</label><input type="text" v-model="form.model" class="form-control text-right" placeholder="مثال: iPhone 13" required></div>
                        </div>
                        <div class="form-row mb-0">
                            <div class="form-group mb-0"><label>نام مشتری (اجباری):</label><input type="text" v-model="form.customerName" class="form-control text-right" placeholder="نام کامل" required></div>
                            <div class="form-group mb-0"><label>شماره تماس:</label><input type="tel" v-model="form.phone" class="form-control text-center" placeholder="0912..." dir="ltr"></div>
                        </div>
                    </div>

                    <div class="modern-section mt-sm">
                        <div class="ms-header text-danger-dark">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>
                            <span>شرح خدمات</span>
                        </div>
                        <div class="form-group">
                            <label>ایراد اعلامی:</label>
                            <input type="text" v-model="form.problem" class="form-control" placeholder="مشکل دستگاه چیست؟" required>
                        </div>
                        <div class="form-group mb-0">
                            <label>شرح خدمات و قطعات (اختیاری):</label>
                            <input type="text" v-model="form.replacedParts" class="form-control" placeholder="اقدامات انجام شده...">
                        </div>
                    </div>

                    <div class="modern-section mt-sm">
                        <div class="ms-header" style="color: #059669;">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="1" x2="12" y2="23"></line><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path></svg>
                            <span>اطلاعات مالی</span>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>هزینه قطعات:</label>
                                <div class="input-with-currency">
                                    <input type="text" :value="formatNumber(form.partsCost)" @input="e => handleNumberInput('partsCost', e)" class="form-control text-center font-800" dir="ltr" placeholder="0">
                                    <span class="currency-addon">تومان</span>
                                </div>
                            </div>
                            <div class="form-group">
                                <label>مبلغ کل فاکتور:</label>
                                <div class="input-with-currency">
                                    <input type="text" :value="formatNumber(form.totalPrice)" @input="e => handleNumberInput('totalPrice', e)" class="form-control text-center text-success-dark font-900 input-highlight" required dir="ltr">
                                    <span class="currency-addon text-success-dark">تومان</span>
                                </div>
                            </div>
                        </div>
                        <div class="form-row mb-0">
                            <div class="form-group mb-0">
                                <label>سهم همکار:</label>
                                <div class="input-with-currency">
                                    <input type="text" :value="formatNumber(form.partnerShare)" @input="e => handleNumberInput('partnerShare', e)" class="form-control text-center font-800" dir="ltr" placeholder="0">
                                    <span class="currency-addon">تومان</span>
                                </div>
                            </div>
                            <div class="form-group mb-0 d-flex align-center justify-center" style="background: var(--bg-color); border-radius: 12px; border: 1px solid var(--border);">
                                <label class="d-flex align-center gap-sm m-0" style="cursor: pointer; width: 100%; padding: 0 10px; height: 100%;">
                                    <input type="checkbox" v-model="form.isCredit" style="width: 18px; height: 18px; accent-color: var(--danger);">
                                    <span class="text-sm font-900" :class="form.isCredit ? 'text-danger-dark' : 'text-muted'">ثبت نسیه</span>
                                </label>
                            </div>
                        </div>
                        
                        <div class="form-group mt-md mb-0" v-if="form.isCredit" style="animation: fadeIn 0.3s ease;">
                            <label class="text-danger-dark font-900">دریافتی علی‌الحساب:</label>
                            <div class="input-with-currency">
                                <input type="text" :value="formatNumber(form.initialPayment)" @input="e => handleNumberInput('initialPayment', e)" class="form-control text-center font-900" dir="ltr" placeholder="0" style="border-color: #fca5a5; background: #fef2f2; color: #b91c1c;">
                                <span class="currency-addon" style="color: #b91c1c;">تومان</span>
                            </div>
                        </div>
                        
                        <div class="install-result-box mt-md" v-if="form.totalPrice > 0" :style="(form.totalPrice - form.partsCost - form.partnerShare) < 0 ? 'background: #fff1f2; border-color: #fecdd3;' : 'background: #f0fdf4; border-color: #bbf7d0;'">
                            <div class="d-flex justify-between align-center m-0" style="white-space: nowrap; height: 100%;">
                                <div class="d-flex align-center" :class="(form.totalPrice - form.partsCost - form.partnerShare) < 0 ? 'text-danger-dark' : 'text-success-dark'">
                                    <span class="font-900" style="font-size: 13.5px;">اجرت خالص:</span>
                                </div>
                                <div class="d-flex align-center" style="direction: rtl; gap: 4px;" :class="(form.totalPrice - form.partsCost - form.partnerShare) < 0 ? 'text-danger-dark' : 'text-success-dark'">
                                    <strong class="font-900 dir-ltr m-0 p-0" style="font-size: 18px; line-height: 1;">{{ formatNumber(form.totalPrice - form.partsCost - form.partnerShare) }}</strong>
                                    <span class="m-0 p-0" style="font-size: 11px; font-weight: 800;">تومان</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="d-flex gap-md" style="margin-top: 40px !important;">
                        <button type="submit" class="btn btn-primary mt-0 flex-2 shadow-primary" style="background: #0d9488;">{{ editId ? 'ذخیره تغییرات' : 'ثبت نهایی' }}</button>
                        <button type="button" @click="closeForm" class="btn btn-secondary mt-0 flex-1">انصراف</button>
                    </div>
                </form>
            </div>
        </div>

        <!-- لیست اصلی تعمیرات -->
        <div v-show="!isFormOpen">
            <div v-if="filteredRepairs.length === 0" class="empty-state-app">
                <div class="empty-icon">🛠️</div>
                <p>{{ searchQuery ? 'نتیجه‌ای یافت نشد.' : 'هیچ پرونده تعمیری ثبت نشده است.' }}</p>
            </div>
            
            <div v-else class="app-list-wrapper">
                <div v-for="repair in filteredRepairs" :key="repair.id" 
                     class="app-card clickable" 
                     :class="{
                         'overdue-highlight': getFinancials(repair).debt > 0 && repair.isCredit,
                         'settled-highlight': getFinancials(repair).debt <= 0 && repair.isCredit
                     }"
                     @click="openDetails(repair)">
                     
                    <div class="sc-header-modern">
                        <div class="sc-right-info">
                            <div class="sc-meta-row mb-sm">
                                <span class="sc-invoice-badge" style="background: #f0fdfa; color: #0f766e; border-color: #ccfbf1;">شماره: <span class="dir-ltr d-inline-block">R-{{ repair.repairInvoiceNumber }}</span></span>
                                <span class="sc-date-text dir-ltr">{{ repair.date }}</span>
                            </div>
                            <strong class="sc-name-text mb-xs">{{ repair.customerName }}</strong>
                            <div class="sc-item-box" style="background: #f8fafc; color: #475569; border-color: #cbd5e1;">{{ repair.model }}</div>
                        </div>
                        
                        <div class="sc-left-badges">
                            <span class="sc-badge-type" :class="repair.isCredit ? 'type-credit' : 'type-cash'">
                                {{ repair.isCredit ? 'نسیه' : 'نقدی' }}
                            </span>
                            <span class="sc-badge-status mt-xs" :class="getFinancials(repair).debt <= 0 ? 'status-settled' : 'status-debt'">
                                <svg v-if="getFinancials(repair).debt <= 0" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path><polyline points="22 4 12 14.01 9 11.01"></polyline></svg>
                                <svg v-else width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>
                                {{ getFinancials(repair).debt <= 0 ? 'تسویه کامل' : 'دارای مطالبات' }}
                            </span>
                        </div>
                    </div>
                    
                    <div class="sale-card-footer-grid">
                        <div class="sale-footer-col">
                            <span class="app-footer-label">مبلغ کل فاکتور</span>
                            <div class="d-flex align-center justify-center mt-sm" style="direction: rtl; gap: 4px;">
                                <strong class="app-footer-value text-dark dir-ltr">{{ formatNumber(repair.totalPrice) }}</strong>
                                <span class="currency-label text-muted">تومان</span>
                            </div>
                        </div>
                        <div class="sale-footer-divider"></div>
                        <div class="sale-footer-col">
                            <span class="app-footer-label">اجرت محقق‌شده</span>
                            <div class="d-flex align-center justify-center mt-sm" style="direction: rtl; gap: 4px;">
                                <strong class="app-footer-value dir-ltr" :class="repair.profit >= 0 ? 'text-success-dark' : 'text-danger-dark'">
                                    {{ formatNumber(getFinancials(repair).realizedProfit) }}
                                </strong>
                                <span class="currency-label" :class="repair.profit >= 0 ? 'text-success-dark' : 'text-danger-dark'">تومان</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <button v-show="!isFormOpen" @click="openForm" class="fab-new-sale" title="پذیرش دستگاه جدید" style="background: linear-gradient(135deg, #0ea5e9, #0f766e);">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>
        </button>

        <!-- مودال‌ها -->
        <div v-if="showFilterModal" class="modal-overlay" @click.self="showFilterModal = false">
            <div class="modal-container modal-sm">
                <div class="modal-header">
                    <h3 class="modal-title">انتخاب بازه زمانی</h3>
                    <button class="btn-close" @click="showFilterModal = false"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg></button>
                </div>
                <div class="modal-body filter-list-body">
                    <button v-for="f in filters" :key="f.value" @click="applyFilter(f.value)" class="filter-list-item" :class="{ 'active': currentFilter === f.value }">
                        <span>{{ f.label }}</span>
                        <svg v-if="currentFilter === f.value" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
                    </button>
                </div>
            </div>
        </div>

        <div v-if="showCustomDateModal" class="modal-overlay" style="z-index: 1200;" @click.self="showCustomDateModal = false">
            <div class="modal-container modal-sm">
                <div class="modal-header">
                    <h3 class="modal-title">تاریخ دلخواه</h3>
                    <button class="btn-close" @click="showCustomDateModal = false"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg></button>
                </div>
                <div class="modal-body">
                    <div class="form-group"><label>از تاریخ:</label><input v-model="customStartDate" type="text" class="form-control text-center dir-ltr" placeholder="1402/01/01" /></div>
                    <div class="form-group mb-0"><label>تا تاریخ:</label><input v-model="customEndDate" type="text" class="form-control text-center dir-ltr" placeholder="1402/12/29" /></div>
                </div>
                <div class="modal-footer d-flex gap-sm flex-mobile-col">
                    <button class="btn btn-primary flex-1 m-0" @click="applyCustomFilter">اعمال فیلتر</button>
                    <button class="btn btn-secondary flex-1 m-0" @click="showCustomDateModal = false">انصراف</button>
                </div>
            </div>
        </div>

        <!-- داشبورد آمار تعمیرات -->
        <div v-if="showStatsModal" class="modal-overlay glass-overlay" style="z-index: 10000;" @click.self="showStatsModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>
                <div class="modal-top-bar px-sm mb-md">
                    <div class="d-flex align-center gap-sm">
                        <div style="width:46px; height:46px; border-radius:14px; background:#ccfbf1; color:#0f766e; display:flex; align-items:center; justify-content:center; flex-shrink: 0;">
                            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path></svg>
                        </div>
                        <div>
                            <h3 class="m-0 text-dark" style="font-size: 17px; font-weight: 900;">گزارش مالی تعمیرات</h3>
                            <span class="text-muted" style="font-size: 12px; font-weight: 700;">{{ filterLabel }}</span>
                        </div>
                    </div>
                    <button class="btn-circular-close bg-light" @click="showStatsModal = false">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                    </button>
                </div>
                
                <div class="modern-sheet-body hide-scroll px-sm pb-xl">
                    <div class="stats-vertical-layout">
                        <div class="stat-box-pro bg-primary-light border-primary text-center">
                            <div class="stat-header text-primary-dark justify-center">
                                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="6"/><circle cx="12" cy="12" r="2"/></svg>
                                <span>مبلغ کل فاکتورها (ناخالص)</span>
                            </div>
                            <div class="stat-value-container text-primary-dark">
                                <strong class="dir-ltr">{{ formatNumber(stats.totalRevenue) }}</strong>
                                <span class="currency-std">تومان</span>
                            </div>
                        </div>

                        <!-- باکس اجرت دوتایی -->
                        <div class="stat-box-pro bg-success-light border-success p-0" style="overflow: hidden;">
                            <div class="d-flex w-100 align-center">
                                <div class="flex-1 text-center p-md" style="border-left: 1px dashed #a7f3d0;">
                                    <div class="stat-header text-success-dark justify-center mb-xs">
                                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2v20M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/></svg>
                                        <span style="font-size: 11px;">اجرت محقق‌شده</span>
                                    </div>
                                    <div class="stat-value-container text-success-dark mt-xs">
                                        <strong class="dir-ltr" style="font-size: 20px;">{{ formatNumber(stats.totalRealizedProfit) }}</strong>
                                        <span class="currency-std" style="font-size: 10px;">تومان</span>
                                    </div>
                                </div>
                                <div class="flex-1 text-center p-md">
                                    <div class="stat-header text-success-dark justify-center mb-xs" style="opacity: 0.8;">
                                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>
                                        <span style="font-size: 11px;">اجرت در جریان</span>
                                    </div>
                                    <div class="stat-value-container text-success-dark mt-xs" style="opacity: 0.8;">
                                        <strong class="dir-ltr" style="font-size: 18px;">{{ formatNumber(stats.totalUnrealizedProfit) }}</strong>
                                        <span class="currency-std" style="font-size: 10px;">تومان</span>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="stat-box-pro bg-danger-light border-danger text-center" v-if="stats.totalLoss > 0">
                            <div class="stat-header text-danger-dark justify-center">
                                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></svg>
                                <span>زیان خالص سیستم</span>
                            </div>
                            <div class="stat-value-container text-danger-dark" style="margin-bottom: 0;">
                                <strong class="dir-ltr">{{ formatNumber(stats.totalLoss) }}</strong>
                                <span class="currency-std">تومان</span>
                            </div>
                        </div>

                        <div class="stats-horizontal-row">
                            <div class="stat-box-small bg-warning-light border-warning text-center">
                                <div class="stat-header text-warning-dark justify-center">
                                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="5" width="20" height="14" rx="2"/><line x1="2" y1="10" x2="22" y2="10"/></svg>
                                    <span>مطالبات (نسیه)</span>
                                </div>
                                <div class="stat-value-container text-warning-dark">
                                    <strong class="dir-ltr" style="font-size: 20px;">{{ formatNumber(stats.totalDebt) }}</strong>
                                    <span class="currency-std text-warning-dark opacity-80" style="font-size: 11px;">تومان</span>
                                </div>
                            </div>
                            
                            <div class="stat-box-small bg-slate-light border-slate text-center">
                                <div class="stat-header text-slate-dark justify-center">
                                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg>
                                    <span>سهم همکار</span>
                                </div>
                                <div class="stat-value-container text-slate-dark">
                                    <strong class="dir-ltr" style="font-size: 20px;">{{ formatNumber(stats.totalPartnerShare) }}</strong>
                                    <span class="currency-std" style="font-size: 11px;">تومان</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div v-if="showDetailsModal" class="modal-overlay glass-overlay" @click.self="showDetailsModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>
                <div class="modern-sheet-body hide-scroll custom-scroll sheet-body-padded">
                    
                    <div class="modal-profile-header-new">
                        <div class="mph-top-row">
                            <div class="mph-meta-group">
                                <span class="mph-invoice-badge" style="background: #f0fdfa; color: #0f766e; border-color: #ccfbf1;">شماره: <span class="dir-ltr d-inline-block">R-{{ activeRepair?.repairInvoiceNumber }}</span></span>
                                <span class="mph-date"><span class="dir-ltr d-inline-block">{{ activeRepair?.date }}</span></span>
                            </div>
                            <button class="btn-circular-close" @click="showDetailsModal = false">
                                <svg viewBox="0 0 24 24" width="18" height="18" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" fill="none"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                            </button>
                        </div>
                        
                        <div class="mph-customer-row">
                            <div class="mph-avatar" style="background: linear-gradient(135deg, #0ea5e9, #0f766e); box-shadow: 0 4px 15px rgba(15, 118, 110, 0.3);">{{ activeRepair?.customerName?.charAt(0) || 'م' }}</div>
                            <div class="mph-customer-details">
                                <h3 class="mph-name">{{ activeRepair?.customerName }}</h3>
                                <span v-if="activeRepair?.phone" class="mph-phone dir-ltr">
                                    <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path></svg>
                                    {{ activeRepair?.phone }}
                                </span>
                            </div>
                        </div>

                        <div class="mph-item-box" style="background: #f8fafc; border-color: #cbd5e1;">
                            <strong class="mph-item-name" style="color: #475569;">{{ activeRepair?.model }}</strong>
                        </div>
                    </div>

                    <div class="hero-balance-section" style="margin-bottom: 24px;" v-if="activeRepair?.isCredit && getFinancials(activeRepair).debt > 0">
                        <span class="hero-label">مطالبات از مشتری</span>
                        <div class="hero-amount-container text-danger-dark">
                            <strong class="hero-amount dir-ltr">{{ formatNumber(getFinancials(activeRepair).debt) }}</strong><span class="hero-currency">تومان</span>
                        </div>
                    </div>
                    <div class="hero-balance-section" style="margin-bottom: 24px;" v-else>
                        <span class="hero-label">وضعیت پرونده</span>
                        <div class="hero-amount-container text-success-dark"><strong class="hero-amount" style="font-size: 26px;">تسویه کامل</strong></div>
                    </div>

                    <div class="section-title-mod"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"></path><polyline points="3.27 6.96 12 12.01 20.73 6.96"></polyline><line x1="12" y1="22.08" x2="12" y2="12"></line></svg> صورتحساب مالی</div>
                    <div class="solid-stats-grid mb-md">
                        <div class="ss-item ss-border-b ss-border-l"><span class="ss-lbl">هزینه قطعات</span><strong class="ss-val dir-ltr">{{ formatNumber(activeRepair?.partsCost) }}</strong></div>
                        <div class="ss-item ss-border-b ss-border-l"><span class="ss-lbl">سهم همکار</span><strong class="ss-val dir-ltr">{{ formatNumber(activeRepair?.partnerShare) }}</strong></div>
                        <div class="ss-item ss-border-b"><span class="ss-lbl">مبلغ فاکتور</span><strong class="ss-val dir-ltr">{{ formatNumber(activeRepair?.totalPrice) }}</strong></div>
                        
                        <div class="ss-item bg-lightest ss-border-l"><span class="ss-lbl">اجرت در جریان</span><strong class="ss-val text-dark dir-ltr">{{ formatNumber(getFinancials(activeRepair).expectedProfit) }}</strong></div>
                        <div class="ss-item bg-lightest ss-border-l"><span class="ss-lbl">علی‌الحساب</span><strong class="ss-val dir-ltr">{{ formatNumber(activeRepair?.initialPayment) }}</strong></div>
                        <div class="ss-item bg-lightest"><span class="ss-lbl">اجرت محقق‌شده</span><strong class="ss-val text-success-dark dir-ltr" :class="{'text-danger-dark': getFinancials(activeRepair).realizedProfit < 0}">{{ formatNumber(getFinancials(activeRepair).realizedProfit) }}</strong></div>
                    </div>

                    <div class="custom-detail-box bg-danger-lightest mt-md mb-sm">
                        <div class="cdb-header text-danger-dark">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></svg>
                            ایراد اعلامی
                        </div>
                        <div class="cdb-content text-danger-dark">{{ activeRepair?.problem }}</div>
                    </div>

                    <div class="custom-detail-box bg-primary-lightest mb-md" v-if="activeRepair?.replacedParts">
                        <div class="cdb-header text-primary-dark">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path></svg>
                            شرح خدمات و قطعات
                        </div>
                        <div class="cdb-content text-primary-dark">{{ activeRepair.replacedParts }}</div>
                    </div>

                    <div class="quick-pay-box mb-md" v-if="activeRepair?.isCredit && getFinancials(activeRepair).debt > 0">
                        <div class="d-flex align-center gap-sm" dir="rtl">
                            <div class="form-group mb-0 flex-1">
                                <input type="text" :value="formatNumber(quickPayAmount)" @input="handleQuickPayInput" class="quick-pay-input" placeholder="ثبت پرداختی جدید..." maxlength="15">
                            </div>
                            <button class="btn-quick-pay" @click="addPayment" :disabled="!quickPayAmount"><svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg></button>
                        </div>
                    </div>
                    
                    <div class="payments-timeline-wrapper" v-if="activeRepair?.isCredit && activeRepair?.payments?.length > 0">
                        <div class="timeline-header">
                            <div class="th-icon"><svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline></svg></div>
                            <h4 class="th-title">تاریخچه واریزی‌ها</h4>
                            <div class="th-badge">{{ activeRepair.payments.length }} مورد</div>
                        </div>
                        <div class="timeline-list">
                            <div v-for="(p, index) in activeRepair.payments" :key="p.id" class="tl-item">
                                <div class="tl-marker"><span>{{ index + 1 }}</span></div>
                                <div class="tl-content">
                                    <div class="tl-amount-group d-flex align-center gap-xs" style="direction: rtl;">
                                        <strong class="tl-amount dir-ltr text-dark">{{ formatNumber(p.amount) }}</strong>
                                        <span class="tl-currency text-muted">تومان</span>
                                    </div>
                                    <div class="tl-date-group"><span class="tl-date dir-ltr">{{ p.date }}</span><button class="tl-del-btn" @click="deletePayment(activeRepair.id, p.id)" title="حذف"><svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg></button></div>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="d-flex gap-sm mt-xl pt-xl" style="border-top: 1px dashed #cbd5e1; margin-top: 36px; padding-top: 24px;">
                        <button class="btn btn-primary flex-1 m-0" style="background: #f0fdfa; color: #0f766e; border-radius: 12px; box-shadow: none;" @click="editRepair(activeRepair)">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="margin-left: 6px;"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path></svg>
                            ویرایش پرونده
                        </button>
                        <button class="btn btn-danger flex-1 m-0" style="background: #fff1f2; color: #e11d48; border-radius: 12px; box-shadow: none;" @click="deleteRepair(activeRepair.id)">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="margin-left: 6px;"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path></svg>
                            حذف کامل
                        </button>
                    </div>

                </div>
            </div>
        </div>

    </div>
</template>


<script>
const { ref, reactive, computed, onMounted, onActivated, nextTick } = Vue;

export default {
    setup() {
        const { formatNumber, unformatNumber, getRepairFinancials, getPersianDate, toEnglishDigits } = window.SysUtils;

        const repairs = ref([]);
        const isFormOpen = ref(false);
        const editId = ref(null);
        
        const showStatsModal = ref(false);
        const showFilterModal = ref(false);
        const showCustomDateModal = ref(false);
        const showDetailsModal = ref(false);
        const activeRepair = ref(null);
        
        const currentFilter = ref('all');
        const searchInput = ref('');
        const searchQuery = ref('');
        const customStartDate = ref('');
        const customEndDate = ref('');
        let debounceTimer;
        
        const quickPayAmount = ref('');

        const filters = [
            { label: 'نمایش همه', value: 'all' },
            { label: '۳ ماه گذشته', value: '3m' },
            { label: '۱ ماه گذشته', value: '1m' },
            { label: '۱ هفته گذشته', value: '1w' },
            { label: 'فقط امروز', value: '1d' },
            { label: 'تاریخ دلخواه...', value: 'custom' }
        ];

        const form = reactive({
            date: '', customerName: '', phone: '', model: '', problem: '', replacedParts: '',
            totalPrice: '', partsCost: '', partnerShare: '', initialPayment: '', isCredit: false
        });

        const handleQuickPayInput = (e) => {
            const el = e.target;
            const cursorPos = el.selectionStart;
            const oldLen = el.value.length;
            
            quickPayAmount.value = unformatNumber(el.value);
            
            nextTick(() => {
                if (el) {
                    const newLen = el.value.length;
                    const newPos = Math.max(0, cursorPos + (newLen - oldLen));
                    el.setSelectionRange(newPos, newPos);
                }
            });
        };

        const handleNumberInput = (field, e) => {
            const el = e.target;
            const cursorPos = el.selectionStart;
            const oldLen = el.value.length;
            
            form[field] = unformatNumber(el.value);
            
            nextTick(() => {
                if (el) {
                    const newLen = el.value.length;
                    const newPos = Math.max(0, cursorPos + (newLen - oldLen));
                    el.setSelectionRange(newPos, newPos);
                }
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
                repairs.value = await window.DB.getAll('repairs');
                repairs.value.sort((a, b) => b.timestamp - a.timestamp);
                
                let needsUpdate = false;
                repairs.value = repairs.value.map((r, index) => {
                    if(!r.repairInvoiceNumber) {
                        r.repairInvoiceNumber = repairs.value.length - index;
                        needsUpdate = true;
                    }
                    return r;
                });
                
                if(needsUpdate) {
                    for(const r of repairs.value) {
                         if(!r.repairInvoiceNumber) window.DB.put('repairs', r);
                    }
                }

                if (activeRepair.value) {
                    const updatedRepair = repairs.value.find(r => r.id === activeRepair.value.id);
                    if (updatedRepair) activeRepair.value = updatedRepair;
                }
            }
        };

        const applyFilter = (filterValue) => { 
            if (filterValue === 'custom') {
                showFilterModal.value = false;
                showCustomDateModal.value = true;
            } else {
                currentFilter.value = filterValue; 
                showFilterModal.value = false;
            }
        };

        const applyCustomFilter = () => {
            if (!customStartDate.value && !customEndDate.value) {
                alert('لطفاً حداقل یک تاریخ را وارد کنید.');
                return;
            }
            currentFilter.value = 'custom';
            showCustomDateModal.value = false;
        };

        const filterLabel = computed(() => {
            if (currentFilter.value === 'custom') return 'تاریخ سفارشی';
            const found = filters.find(f => f.value === currentFilter.value);
            return found ? found.label : 'همه';
        });

        const subtractDaysPersian = (dateStr, days) => {
            if (!dateStr) return '';
            let parts = dateStr.split('/');
            if (parts.length !== 3) return dateStr;
            let y = parseInt(parts[0]), m = parseInt(parts[1]), d = parseInt(parts[2]);
            d -= days;
            while (d <= 0) {
                m -= 1;
                if (m <= 0) {
                    y -= 1;
                    m += 12;
                }
                let daysInMonth = (m <= 6) ? 31 : (m <= 11 ? 30 : 29);
                d += daysInMonth;
            }
            return `${y}/${m.toString().padStart(2, '0')}/${d.toString().padStart(2, '0')}`;
        };

        const subtractMonthsPersian = (dateStr, months) => {
            if (!dateStr) return '';
            let parts = dateStr.split('/');
            if (parts.length !== 3) return dateStr;
            let y = parseInt(parts[0]), m = parseInt(parts[1]), d = parseInt(parts[2]);
            m -= months;
            while (m <= 0) {
                m += 12;
                y -= 1;
            }
            return `${y}/${m.toString().padStart(2, '0')}/${d.toString().padStart(2, '0')}`;
        };

        const filteredRepairs = computed(() => {
            let result = repairs.value;
            
            if (searchQuery.value) {
                const q = searchQuery.value.toLowerCase();
                result = result.filter(r => 
                    (r.customerName && r.customerName.toLowerCase().includes(q)) ||
                    (r.model && r.model.toLowerCase().includes(q)) ||
                    (r.problem && r.problem.toLowerCase().includes(q)) ||
                    (r.repairInvoiceNumber && r.repairInvoiceNumber.toString().includes(q))
                );
            }

            if (currentFilter.value === 'all') return result;
            
            if (currentFilter.value === 'custom') {
                return result.filter(r => {
                    const rDate = r.date || '';
                    let match = true;
                    if (customStartDate.value && rDate < customStartDate.value) match = false;
                    if (customEndDate.value && rDate > customEndDate.value) match = false;
                    return match;
                });
            }

            const today = getPersianDate();
            let targetDate = today;

            if (currentFilter.value === '1d') {
                targetDate = today;
            } else if (currentFilter.value === '1w') {
                targetDate = subtractDaysPersian(today, 7);
            } else if (currentFilter.value === '1m') {
                targetDate = subtractMonthsPersian(today, 1);
            } else if (currentFilter.value === '3m') {
                targetDate = subtractMonthsPersian(today, 3);
            }

            return result.filter(r => {
                const rDate = r.date || '';
                return rDate >= targetDate;
            });
        });

        const stats = computed(() => {
            let totalRevenue = 0, totalRealizedProfit = 0, totalExpectedProfit = 0, totalUnrealizedProfit = 0, totalDebt = 0, totalPartnerShare = 0, totalLoss = 0;

            filteredRepairs.value.forEach(r => {
                const fin = getRepairFinancials(r);
                totalRevenue += fin.finalPrice;
                totalRealizedProfit += fin.realizedProfit;
                totalExpectedProfit += fin.expectedProfit;
                totalPartnerShare += Number(r.partnerShare || 0);
                
                if (fin.realizedProfit < 0) {
                    totalLoss += Math.abs(fin.realizedProfit);
                }
                
                if (fin.debt > 0 && fin.expectedProfit > 0) {
                    const unrealized = fin.expectedProfit - Math.max(0, fin.realizedProfit);
                    totalUnrealizedProfit += Math.max(0, unrealized);
                }

                if (r.isCredit) {
                    totalDebt += fin.debt;
                }
            });

            return { totalRevenue, totalRealizedProfit, totalExpectedProfit, totalUnrealizedProfit, totalDebt, totalPartnerShare, totalLoss };
        });

        const openForm = () => {
            Object.assign(form, {
                date: getPersianDate(), customerName: '', phone: '', model: '', problem: '', replacedParts: '',
                totalPrice: '', partsCost: '', partnerShare: '', initialPayment: '', isCredit: false
            });
            editId.value = null;
            isFormOpen.value = true;
        };

        const closeForm = () => { isFormOpen.value = false; };

        const saveRepair = async () => {
            try {
                if (form.isCredit && Number(form.initialPayment) >= Number(form.totalPrice)) {
                    alert('مبلغ دریافتی علی‌الحساب نمی‌تواند بیشتر یا مساوی مبلغ فاکتور باشد.');
                    return;
                }

                const existingRepair = editId.value ? repairs.value.find(r => r.id === editId.value) : null;
                const baseData = existingRepair ? JSON.parse(JSON.stringify(existingRepair)) : { id: window.DB.generateId(), timestamp: Date.now(), payments: [] };

                if (!existingRepair) {
                    let maxId = 0;
                    repairs.value.forEach(r => {
                        const num = parseInt(r.repairInvoiceNumber);
                        if (!isNaN(num) && num > maxId) maxId = num;
                    });
                    baseData.repairInvoiceNumber = maxId + 1;
                } else {
                    baseData.repairInvoiceNumber = existingRepair.repairInvoiceNumber;
                }
                
                baseData.model = form.model;
                baseData.customerName = form.customerName || 'مشتری بدون نام';
                baseData.date = toEnglishDigits(form.date);
                baseData.phone = toEnglishDigits(form.phone);
                baseData.problem = form.problem;
                baseData.replacedParts = form.replacedParts || '';
                baseData.totalPrice = Number(form.totalPrice) || 0;
                baseData.partsCost = Number(form.partsCost) || 0;
                baseData.partnerShare = Number(form.partnerShare) || 0;
                baseData.isCredit = form.isCredit;
                
                baseData.profit = baseData.totalPrice - baseData.partsCost - baseData.partnerShare;

                // ثبت نقدی خودکار تسویه می‌شود
                if (baseData.isCredit) {
                    baseData.initialPayment = Number(form.initialPayment) || 0;
                } else {
                    baseData.initialPayment = baseData.totalPrice; 
                    baseData.payments = [];
                }

                await window.DB.put('repairs', baseData);
                closeForm();
                await loadData();
            } catch (err) {
                alert('خطا در ذخیره اطلاعات!');
                console.error("Save Error:", err);
            }
        };

        const deleteRepair = async (id) => {
            if (confirm("آیا از حذف کامل این پرونده اطمینان دارید؟")) {
                await window.DB.delete('repairs', id);
                showDetailsModal.value = false;
                await loadData();
            }
        };

        const editRepair = (repair) => {
            showDetailsModal.value = false;
            activeRepair.value = repair;
            editId.value = repair.id;
            Object.assign(form, {
                date: repair.date, customerName: repair.customerName, phone: repair.phone, 
                model: repair.model, problem: repair.problem, replacedParts: repair.replacedParts || '',
                totalPrice: repair.totalPrice, partsCost: repair.partsCost || 0, partnerShare: repair.partnerShare || 0,
                initialPayment: repair.isCredit ? (repair.initialPayment || '') : '', isCredit: repair.isCredit
            });
            isFormOpen.value = true;
        };

        const openDetails = (repair) => {
            activeRepair.value = repair;
            quickPayAmount.value = ''; 
            showDetailsModal.value = true;
        };

        const addPayment = async () => {
            if (!activeRepair.value) return;
            const amt = Number(quickPayAmount.value);
            if (amt <= 0) return alert('مبلغ نامعتبر است');
            
            const currentDebt = getRepairFinancials(activeRepair.value).debt;
            if (amt > currentDebt) return alert('مبلغ پرداختی بیشتر از مانده مطالبات است.');

            const repair = JSON.parse(JSON.stringify(activeRepair.value));
            if (!repair.payments) repair.payments = [];
            
            repair.payments.push({
                id: Date.now(),
                amount: amt,
                date: getPersianDate()
            });
            
            await window.DB.put('repairs', repair);
            quickPayAmount.value = '';
            await loadData();
        };

        const deletePayment = async (repairId, paymentId) => {
            if (!confirm('آیا از حذف این پرداختی اطمینان دارید؟')) return;
            const repair = JSON.parse(JSON.stringify(repairs.value.find(r => r.id === repairId)));
            if (!repair || !repair.payments) return;

            repair.payments = repair.payments.filter(p => p.id !== paymentId);
            await window.DB.put('repairs', repair);
            await loadData();
        };

        onMounted(() => { loadData(); });
        onActivated(() => { loadData(); });

        return {
            repairs, isFormOpen, editId, form, currentFilter, searchInput, searchQuery, onSearchInput, clearSearch, filters, filteredRepairs, 
            stats, showDetailsModal, activeRepair, quickPayAmount,
            showStatsModal, showFilterModal, showCustomDateModal, customStartDate, customEndDate,
            filterLabel, applyFilter, applyCustomFilter, handleNumberInput, handleQuickPayInput,
            formatNumber, unformatNumber, openForm, closeForm, saveRepair, deleteRepair, editRepair, 
            openDetails, addPayment, deletePayment, getFinancials: getRepairFinancials
        };
    }
}
</script>


<style scoped>
.form-container-anim { animation: slideDown 0.3s ease-out; }
@keyframes slideDown { from { opacity: 0; transform: translateY(-10px); } to { opacity: 1; transform: translateY(0); } }
.top-action-bar { display: flex; gap: 8px; width: 100%; align-items: stretch; margin-bottom: 16px; }
.stats-banner-card { flex: 1; border-radius: 16px; padding: 14px 16px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; color: white; box-shadow: 0 6px 15px rgba(15, 118, 110, 0.25); transition: all 0.3s ease; }
.stats-banner-card:active { transform: scale(0.97); }
.sbc-icon { background: rgba(255,255,255,0.2); width: 38px; height: 38px; border-radius: 12px; display: flex; align-items: center; justify-content: center; }
.sbc-text { display: flex; flex-direction: column; }
.sbc-title { font-size: 14px; font-weight: 900; }
.sbc-subtitle { font-size: 10px; opacity: 0.9; }
.sbc-arrow { background: rgba(255,255,255,0.15); width: 28px; height: 28px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
.compact-filter-btn { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 0 12px; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 4px; cursor: pointer; color: var(--text-dark); box-shadow: 0 4px 10px rgba(0,0,0,0.03); min-width: 70px; transition: var(--transition); }
.compact-filter-btn:active { transform: scale(0.95); background: var(--bg-color); }
.cf-label { font-size: 9.5px; font-weight: 800; white-space: nowrap; max-width: 60px; overflow: hidden; text-overflow: ellipsis;}
.app-search-wrapper { width: 100%; margin-bottom: 16px; }
.app-search-inner { position: relative; display: flex; align-items: center; background: var(--surface); border-radius: 24px; box-shadow: 0 4px 16px rgba(0,0,0,0.03); border: 1px solid #f3f4f6; }
.app-search-icon { position: absolute; right: 16px; width: 18px; color: #9ca3af; }
.app-search-input { width: 100%; border: none; background: transparent; padding: 14px 44px 14px 16px; font-size: 13px; font-weight: 700; outline: none; }
.app-search-clear { position: absolute; left: 12px; background: #f3f4f6; color: #6b7280; border: none; border-radius: 50%; width: 24px; height: 24px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
.empty-state-app { text-align: center; padding: 40px 20px; color: #9ca3af; }
.empty-icon { font-size: 40px; margin-bottom: 12px; filter: grayscale(1); opacity: 0.5; }

/* ------ استایل‌های یکپارچه کارت‌های لیست تعمیرات ------ */
.app-list-wrapper { display: flex; flex-direction: column; gap: 14px; margin-bottom: 20px; }

.app-card { 
    background: #ffffff; border-radius: 22px; padding: 22px 20px; 
    box-shadow: 0 6px 20px rgba(0,0,0,0.025); transition: transform 0.2s ease, box-shadow 0.2s; 
    border: 1px solid #f1f5f9; cursor: pointer; display: flex; flex-direction: column; justify-content: space-between;
    border-right: 4px solid transparent;
}
.app-card.clickable:active { transform: scale(0.98); border-color: var(--primary-light); }
.app-card.overdue-highlight { border-right: 4px solid #ef4444; background: linear-gradient(270deg, #fffbfb 0%, #ffffff 100%); box-shadow: 0 4px 15px rgba(239, 68, 68, 0.15); }
.app-card.settled-highlight { border-right: 4px solid #10b981; background: linear-gradient(270deg, #ecfdf5 0%, #ffffff 100%); box-shadow: 0 4px 15px rgba(16, 185, 129, 0.1); }

/* ساختار هدر کارت در لیست */
.sc-header-modern { display: flex; justify-content: space-between; align-items: flex-start; gap: 8px; margin-bottom: 20px; width: 100%; }
.sc-right-info { display: flex; flex-direction: column; align-items: flex-start; text-align: right; flex: 1; min-width: 0; overflow: hidden; }
.sc-left-badges { display: flex; flex-direction: column; align-items: flex-end; gap: 8px; flex-shrink: 0; }

.sc-meta-row { display: flex; align-items: center; gap: 12px; margin-bottom: 8px; width: 100%; }
.sc-invoice-badge { font-size: 11px; font-weight: 800; background: #f1f5f9; color: #475569; padding: 4px 10px; border-radius: 8px; border: 1px solid #e2e8f0; }
.sc-date-text { font-size: 11px; font-weight: 800; color: #94a3b8; }
.sc-name-text { font-size: 17px; font-weight: 900; color: #0f172a; margin-bottom: 6px; line-height: 1.4; display: block; }
.sc-item-box { white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 100%; font-size: 12.5px; font-weight: 900; color: var(--primary-dark); background: var(--primary-light); padding: 5px 12px; border-radius: 8px; display: inline-block; align-self: flex-start; border: 1px dashed #bfdbfe; }

.sc-badge-type { font-size: 10px; font-weight: 800; padding: 4px 10px; border-radius: 8px; text-align: center; }
.type-cash { background: var(--success-light); color: var(--success-dark); border: 1px solid #a7f3d0; }
.type-credit { background: var(--warning-light); color: var(--warning-dark); border: 1px solid #fde68a; }

.sc-badge-status { font-size: 10.5px; font-weight: 800; padding: 4px 10px; border-radius: 8px; display: flex; align-items: center; gap: 4px; text-align: center; }
.status-settled { background: #d1fae5; color: #047857; }
@keyframes pulseAlert { 0% { opacity: 1; transform: scale(1); } 50% { opacity: 0.6; transform: scale(1.1); } 100% { opacity: 1; transform: scale(1); } }
.status-debt { background: #fee2e2; color: #dc2626; border: 1px solid #fca5a5; box-shadow: 0 2px 6px rgba(239,68,68,0.2); }
.status-debt svg { animation: pulseAlert 1.5s infinite; }

/* Grid متقارن فوتر */
.sale-card-footer-grid { display: grid; grid-template-columns: 1fr 1px 1fr; align-items: center; background: #f8fafc; padding: 14px 10px; border-radius: 16px; width: 100%; border: 1px solid #f1f5f9; }
.sale-footer-col { display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 4px; text-align: center; }
.sale-footer-divider { width: 1px; height: 30px; background: #e2e8f0; margin: 0 auto; }
.app-footer-label { font-size: 10px; color: #9ca3af; font-weight: 700; margin: 0; white-space: nowrap; }
.app-footer-value { font-size: 16px; font-weight: 900; line-height: 1; margin-top: 2px; }
.currency-label { font-size: 11px; font-weight: 800; margin-top: 2px; margin-right: 0; } 

/* ------ استایل‌های هدر جدید مودال جزئیات ------ */
.modal-profile-header-new {
    display: flex;
    flex-direction: column;
    gap: 14px;
    background: #ffffff;
    border: 1px solid #e2e8f0;
    border-radius: 20px;
    padding: 18px;
    margin-bottom: 24px;
    direction: rtl;
    text-align: right;
    box-shadow: 0 4px 15px -5px rgba(0,0,0,0.03);
}
.mph-top-row {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    width: 100%;
    border-bottom: 1px dashed #e2e8f0;
    padding-bottom: 14px;
}
.mph-meta-group {
    display: flex;
    align-items: center;
    gap: 12px;
}
.mph-invoice-badge {
    font-size: 11.5px;
    font-weight: 900;
    background: var(--primary-light);
    color: var(--primary-dark);
    padding: 4px 10px;
    border-radius: 8px;
    border: 1px solid #bfdbfe;
    display: flex;
    align-items: center;
    gap: 4px;
}
.mph-date {
    font-size: 11.5px;
    font-weight: 800;
    color: #64748b;
}
.mph-customer-row {
    display: flex;
    align-items: center;
    gap: 14px;
}
.mph-avatar {
    width: 56px;
    height: 56px;
    border-radius: 18px;
    background: linear-gradient(135deg, #0ea5e9, #0f766e);
    color: white;
    font-size: 26px;
    font-weight: 900;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 6px 15px rgba(129, 140, 248, 0.4);
    flex-shrink: 0;
}
.mph-customer-details {
    display: flex;
    flex-direction: column;
    gap: 6px;
}
.mph-name {
    font-size: 18px;
    font-weight: 900;
    color: #0f172a;
    margin: 0;
}
.mph-phone {
    font-size: 11.5px;
    font-weight: 800;
    color: #475569;
    background: #f8fafc;
    border: 1px solid #cbd5e1;
    padding: 3px 10px;
    border-radius: 8px;
    display: inline-flex;
    align-items: center;
    gap: 4px;
    align-self: flex-start;
}
.mph-item-box {
    background: #eff6ff;
    border: 1px dashed #bfdbfe;
    border-radius: 12px;
    padding: 12px;
    display: flex;
    justify-content: center;
    align-items: center;
}
.mph-item-name {
    color: #1e40af;
    font-weight: 900;
    font-size: 15px;
}

/* باکَس‌های شرح ایراد و قطعات */
.custom-detail-box { border-radius: 14px; padding: 14px; }
.bg-danger-lightest { background: #fff5f5; border: 1px dashed #fca5a5; }
.bg-primary-lightest { background: #eff6ff; border: 1px dashed #93c5fd; }
.cdb-header { display: flex; align-items: center; gap: 6px; font-size: 11px; font-weight: 900; margin-bottom: 8px; opacity: 0.85; }
.cdb-content { font-size: 13.5px; font-weight: 800; line-height: 1.7; padding-right: 22px; }

/* ---------------------------------------------------- */
.modern-form .form-row { display: flex; gap: 12px; margin-bottom: 12px; }
.modern-form .form-row .form-group { flex: 1; margin-bottom: 0; }
.modern-form .form-control { min-height: 40px; padding: 8px 12px; font-size: 12.5px; background: white; border: 1px solid #cbd5e1; border-radius: 12px; transition: all 0.25s ease; box-shadow: inset 0 2px 4px rgba(0,0,0,0.01); }
.modern-form .form-control:focus { border-color: #0d9488; box-shadow: 0 0 0 3px #ccfbf1; }
.modern-form .form-group label { display: flex; align-items: center; gap: 4px; font-size: 10.5px; font-weight: 800; color: #64748b; margin-bottom: 6px; }
.input-with-currency { position: relative; display: flex; align-items: center; }
.input-with-currency .form-control { padding-left: 45px; }
.currency-addon { position: absolute; left: 12px; font-size: 10px; font-weight: 800; color: #94a3b8; pointer-events: none; }
.input-highlight { background: #f0fdf4 !important; border-color: #86efac !important; color: var(--success-dark) !important; }
.input-highlight:focus { box-shadow: 0 0 0 3px #dcfce7 !important; }
.modern-section { background: #f8fafc; border: 1px solid #e2e8f0; border-radius: 16px; padding: 14px; position: relative; }
.ms-header { display: flex; align-items: center; gap: 6px; margin-bottom: 14px; font-size: 13px; font-weight: 900; }
.shadow-primary { box-shadow: 0 4px 12px rgba(15, 118, 110, 0.25); }

.install-result-box { background: white; border-radius: 12px; padding: 12px 14px; display: flex; flex-direction: column; gap: 8px; border: 1px solid transparent; transition: 0.3s; }
.ir-row { display: flex; justify-content: space-between; font-size: 11.5px; color: var(--text-dark); align-items: center; }

.filter-list-body { display: flex; flex-direction: column; gap: 8px; padding: 16px !important; }
.filter-list-item { display: flex; justify-content: space-between; align-items: center; width: 100%; padding: 12px 16px; background: var(--bg-color); border: 1px solid var(--border); border-radius: 12px; font-size: 12px; font-weight: 800; color: var(--text-muted); cursor: pointer; transition: var(--transition); }
.filter-list-item.active { background: #ccfbf1; color: #0f766e; border-color: #5eead4; }
.filter-list-item:active { transform: scale(0.98); }
.relative-page { position: relative; min-height: 100vh; padding-bottom: 120px; }
.fab-new-sale { position: fixed; bottom: 115px; left: 20px; z-index: 900; width: 56px; height: 56px; border-radius: 18px; color: white; border: none; display: flex; align-items: center; justify-content: center; box-shadow: 0 10px 25px -5px rgba(15, 118, 110, 0.5); cursor: pointer; transition: var(--transition); }
.fab-new-sale:active { transform: scale(0.9) translateY(4px); box-shadow: 0 4px 10px rgba(15, 118, 110, 0.4); }

.stat-value-container { display: flex; align-items: center; justify-content: center; gap: 5px; direction: rtl; margin-top: 6px; width: 100%; }
.stat-value-container strong { font-size: 28px; font-weight: 900; letter-spacing: -0.5px; line-height: 1; margin: 0; padding: 0; white-space: nowrap; }
.stat-value-container .currency-std { font-size: 13px; font-weight: 800; opacity: 0.9; margin: 0; padding: 0; white-space: nowrap; }

.stats-vertical-layout { display: flex; flex-direction: column; gap: 14px; width: 100%; }
.stats-horizontal-row { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; width: 100%; direction: rtl; }
.stat-box-pro { border-radius: 20px; padding: 16px 14px; border: 1px solid transparent; display: flex; flex-direction: column; align-items: center; text-align: center; overflow: hidden; min-width: 0; box-shadow: 0 4px 12px rgba(0,0,0,0.03); direction: rtl; }
.stat-box-small { border-radius: 20px; padding: 14px 10px; border: 1px solid transparent; display: flex; flex-direction: column; align-items: center; text-align: center; overflow: hidden; min-width: 0; box-shadow: 0 4px 12px rgba(0,0,0,0.03); direction: rtl; }
.stat-header { display: flex; align-items: center; justify-content: center; gap: 8px; font-size: 13.5px; font-weight: 900; margin-bottom: 8px; width: 100%; text-align: center; white-space: nowrap; }
.stat-box-small .stat-header { justify-content: center; margin-bottom: 6px; font-size: 11.5px; }

.bg-primary-light { background: #eff6ff; } .border-primary { border-color: #bfdbfe; } .text-primary-dark { color: #1e40af; }
.bg-success-light { background: #f0fdf4; } .border-success { border-color: #bbf7d0; } .text-success-dark { color: #166534; }
.bg-danger-light { background: #fef2f2; } .border-danger { border-color: #fecaca; } .text-danger-dark { color: #991b1b; }
.bg-warning-light { background: #fffbeb; } .border-warning { border-color: #fde68a; } .text-warning-dark { color: #92400e; }
.bg-slate-light { background: #f8fafc; } .border-slate { border-color: #e2e8f0; } .text-slate-dark { color: #0f172a; }
.glass-overlay { align-items: flex-end; padding: 0; background: rgba(17, 24, 39, 0.45); backdrop-filter: blur(6px); display: flex; justify-content: center; z-index: 10000; position: fixed !important; inset: 0 !important; }
.modern-bottom-sheet { background: #ffffff; width: 100%; max-width: 600px; border-radius: 36px 36px 0 0; padding: 20px 12px 30px 12px; animation: slideUpModern 0.4s cubic-bezier(0.2, 0.8, 0.2, 1); display: flex; flex-direction: column; max-height: 92vh; box-shadow: 0 -10px 40px rgba(0,0,0,0.1); position: relative; }
@keyframes slideUpModern { from { transform: translateY(100%); } to { transform: translateY(0); } }
.sheet-drag-handle { width: 48px; height: 5px; background: #e5e7eb; border-radius: 4px; margin: 0 auto 20px auto; flex-shrink: 0; }
.modern-sheet-body { overflow-y: auto; padding-bottom: 20px; }
.hide-scroll::-webkit-scrollbar { display: none; }
.px-sm { padding-left: 12px; padding-right: 12px; }
.modal-top-bar { display: flex; justify-content: space-between; align-items: center; width: 100%; flex-shrink: 0; margin-bottom: 20px; }
.btn-circular-close { width: 36px; height: 36px; border-radius: 50%; background: #f3f4f6; border: none; color: #4b5563; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; margin-right: auto; }
.btn-circular-close:active { transform: scale(0.9); background: #e2e8f0; }

.hero-balance-section { display: flex; flex-direction: column; align-items: center; text-align: center; }
.hero-label { font-size: 12px; color: #6b7280; font-weight: 800; margin-bottom: 6px; }
.hero-amount-container { display: flex; align-items: center; justify-content: center; gap: 5px; direction: rtl; width: 100%; }
.hero-amount { font-size: 38px; font-weight: 900; letter-spacing: -1px; line-height: 1; margin: 0; padding: 0; white-space: nowrap; }
.hero-currency { font-size: 14px; font-weight: 800; opacity: 0.8; margin: 0; padding: 0; white-space: nowrap; }

.section-title-mod { font-size: 13px; font-weight: 900; color: var(--text-dark); margin-bottom: 12px; display: flex; align-items: center; gap: 6px; direction: rtl; }
.solid-stats-grid { background: #ffffff; border: 1px solid #e5e7eb; border-radius: 16px; display: grid; grid-template-columns: repeat(3, 1fr); overflow: hidden; box-shadow: 0 4px 10px rgba(0,0,0,0.02); direction: rtl; }
.ss-item { padding: 12px 4px; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center; gap: 4px; overflow: hidden; }
.ss-border-b { border-bottom: 1px solid #f3f4f6; }
.ss-border-l { border-left: 1px solid #f3f4f6; } 
.bg-lightest { background: #f8fafc; }
.ss-lbl { font-size: 9px; font-weight: 800; color: #9ca3af; white-space: nowrap; }
.ss-val { font-size: 12px; font-weight: 900; color: #1f2937; white-space: nowrap; }
.quick-pay-box { background: #ffffff; border: 1px solid #e5e7eb; border-radius: 16px; padding: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.02); }
.empty-schedule { text-align: center; padding: 16px; font-size: 13px; font-weight: 800; border-radius: 16px; }
.quick-pay-input { font-size: 17px; font-weight: 900; padding: 12px 16px; border-radius: 12px; border: 2px solid #e5e7eb; width: 100%; outline: none; background: #f9fafb; color: #111827; transition: 0.2s; text-align: right; }
.quick-pay-input:focus { border-color: #0d9488; background: white; box-shadow: 0 0 0 3px #ccfbf1; }
.btn-quick-pay { width: 52px; height: 52px; border-radius: 12px; background: #d1d5db; color: #6b7280; border: none; display: flex; align-items: center; justify-content: center; transition: 0.2s; flex-shrink: 0; }
.btn-quick-pay:not(:disabled) { background: #10b981; color: white; cursor: pointer; box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3); }
.btn-quick-pay:not(:disabled):active { transform: scale(0.9); }
.timeline-header { display: flex; align-items: center; gap: 10px; margin-bottom: 16px; direction: rtl; }
.th-icon { width: 32px; height: 32px; border-radius: 10px; background: #f3f4f6; color: #4b5563; display: flex; align-items: center; justify-content: center; }
.th-title { font-size: 15px; font-weight: 900; color: #111827; margin: 0; flex: 1; text-align: right; }
.th-badge { font-size: 11px; font-weight: 800; background: #e0e7ff; color: #1d4ed8; padding: 4px 10px; border-radius: 20px; }
.timeline-list { display: flex; flex-direction: column; gap: 10px; position: relative; padding-right: 12px; direction: rtl; }
.timeline-list::before { content: ''; position: absolute; right: 28px; top: 10px; bottom: 10px; width: 2px; background: #f3f4f6; z-index: 0; }
.tl-item { display: flex; gap: 12px; position: relative; z-index: 1; align-items: center; }
.tl-content { flex: 1; background: #ffffff; border: 1px solid #e5e7eb; border-radius: 12px; padding: 0 12px; height: 46px; display: flex; justify-content: space-between; align-items: center; transition: 0.2s; box-sizing: border-box; }
.tl-content:active { background: #f9fafb; transform: scale(0.98); }
.tl-del-btn { width: 32px; height: 32px; border-radius: 10px; background: #fef2f2; color: #ef4444; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; }
.tl-del-btn:active { background: #fee2e2; transform: scale(0.85); }
.tl-amount-group { display: flex; align-items: center; gap: 6px; }
.tl-date-group { display: flex; align-items: center; gap: 12px; }
.tl-amount { font-size: 17px; font-weight: 900; color: #0f172a; display: block; line-height: 1; transform: translateY(1px); margin: 0; }
.tl-currency { font-size: 11px; font-weight: 800; color: #94a3b8; display: block; line-height: 1; transform: translateY(2px); margin: 0; }
.tl-date { font-size: 12px; font-weight: 800; color: #64748b; display: block; line-height: 1; transform: translateY(1px); margin: 0; }
.tl-marker { width: 32px; height: 32px; border-radius: 10px; background: #ecfdf5; border: 2px solid white; display: flex; align-items: center; justify-content: center; font-size: 13px; font-weight: 900; color: #059669; flex-shrink: 0; box-shadow: 0 2px 6px rgba(5, 150, 105, 0.15); z-index: 2; }
.tl-marker span { transform: translateY(1px); }
</style>
