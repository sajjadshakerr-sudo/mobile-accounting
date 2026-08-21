<template>
    <div class="page active relative-page">
        
        <div class="top-action-bar mb-md">
            <div class="stats-banner-card" @click="showStatsModal = true">
                <div class="d-flex align-center gap-sm">
                    <div class="sbc-icon">
                        <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="20" x2="18" y2="10"></line><line x1="12" y1="20" x2="12" y2="4"></line><line x1="6" y1="20" x2="6" y2="14"></line></svg>
                    </div>
                    <div class="sbc-text">
                        <strong class="sbc-title">گزارش مالی اقساط</strong>
                        <span class="sbc-subtitle">مشاهده مانده مطالبات و وصولی‌ها</span>
                    </div>
                </div>
                <div class="sbc-arrow">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>
                </div>
            </div>
        </div>

        <div class="app-search-wrapper mb-md">
            <div class="app-search-inner">
                <svg class="app-search-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line></svg>
                <input type="text" :value="searchInput" @input="onSearchInput" class="app-search-input" placeholder="جستجوی مشتری، کالا یا فاکتور..." />
                <button v-if="searchInput" class="app-search-clear" @click="clearSearch">
                    <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                </button>
            </div>
        </div>

        <div v-if="processedInstallments.length === 0" class="empty-state-app">
            <div class="empty-icon">📂</div>
            <p>{{ searchQuery ? 'نتیجه‌ای یافت نشد.' : 'هیچ پرونده اقساطی در سیستم ثبت نشده است.' }}</p>
        </div>

        <div class="app-list-wrapper" v-else>
            <div v-for="inst in processedInstallments" :key="inst.id" 
                 class="app-card clickable" 
                 :class="{
                     'overdue-highlight': inst._hasOverdue && inst._debt > 0,
                     'settled-highlight': inst._debt <= 0
                 }"
                 @click="openDetails(inst)">
                 
                <!-- هدر یکپارچه و مدرن -->
                <div class="sc-header-modern">
                    <div class="sc-right-info">
                        <div class="sc-meta-row mb-sm">
                            <span class="sc-invoice-badge">فاکتور: <span class="dir-ltr d-inline-block">{{ inst.invoiceNumber }}</span></span>
                            <span class="sc-date-text dir-ltr">{{ inst.date }}</span>
                        </div>
                        <strong class="sc-name-text mb-xs">{{ inst.customerName || 'مشتری عمومی' }}</strong>
                        <div class="sc-item-box">{{ inst.itemName }}</div>
                    </div>
                    
                    <div class="sc-left-badges">
                        <span class="sc-badge-type type-installment">اقساطی</span>
                        <span class="sc-badge-status mt-xs" :class="inst._debt <= 0 ? 'status-settled' : (inst._hasOverdue ? 'status-debt' : 'status-pending')">
                            <svg v-if="inst._debt <= 0" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path><polyline points="22 4 12 14.01 9 11.01"></polyline></svg>
                            <svg v-else-if="inst._hasOverdue && inst._debt > 0" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>
                            <svg v-else width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>
                            {{ inst._debt <= 0 ? 'تسویه کامل' : (inst._hasOverdue ? 'معوقه' : 'در جریان') }}
                        </span>
                    </div>
                </div>
                
                <div class="app-progress-section mt-md">
                    <div class="app-progress-labels">
                        <span class="app-progress-label">پیشرفت وصولی</span>
                        <strong class="app-progress-percent dir-ltr">{{ Math.round(inst._progress) }}%</strong>
                    </div>
                    <div class="app-progress-track">
                        <div class="app-progress-fill" :style="{ width: inst._progress + '%' }" :class="inst._debt <= 0 ? 'fill-success' : 'fill-primary'"></div>
                    </div>
                </div>
                
                <!-- فوتر متقارن -->
                <div class="sale-card-footer-grid mt-md">
                    <div class="sale-footer-col">
                        <span class="app-footer-label">وصولی کل فاکتور</span>
                        <div class="d-flex align-center justify-center mt-sm" style="direction: rtl; gap: 4px;">
                            <strong class="app-footer-value text-dark dir-ltr">{{ formatNumber(inst._paid) }}</strong>
                            <span class="currency-label text-muted">تومان</span>
                        </div>
                    </div>
                    <div class="sale-footer-divider"></div>
                    <div class="sale-footer-col">
                        <span class="app-footer-label">مانده اقساط</span>
                        <div class="d-flex align-center justify-center mt-sm" style="direction: rtl; gap: 4px;">
                            <strong class="app-footer-value dir-ltr" :class="inst._debt > 0 ? 'text-danger-dark' : 'text-success-dark'">{{ formatNumber(inst._debt) }}</strong>
                            <span class="currency-label" :class="inst._debt > 0 ? 'text-danger-dark' : 'text-success-dark'">تومان</span>
                        </div>
                    </div>
                </div>

            </div>
        </div>

        <!-- پاپ آپ آمار و گزارش اقساط -->
        <div v-if="showStatsModal" class="modal-overlay glass-overlay" style="z-index: 10000;" @click.self="showStatsModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>
                
                <div class="modal-top-bar px-xs mb-md">
                    <div class="d-flex align-center gap-sm">
                        <div style="width:46px; height:46px; border-radius:14px; background:#eff6ff; color:#2563eb; display:flex; align-items:center; justify-content:center; flex-shrink: 0;">
                            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="20" x2="18" y2="10"></line><line x1="12" y1="20" x2="12" y2="4"></line><line x1="6" y1="20" x2="6" y2="14"></line></svg>
                        </div>
                        <div>
                            <h3 class="m-0 text-dark" style="font-size: 17px; font-weight: 900;">داشبورد مالی اقساط</h3>
                            <span class="text-muted" style="font-size: 12px; font-weight: 700;">وضعیت مطالبات و وصولی‌ها</span>
                        </div>
                    </div>
                    <button class="btn-circular-close bg-light" @click="showStatsModal = false">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                    </button>
                </div>
                
                <div class="modern-sheet-body hide-scroll px-xs pb-xl">
                    <div class="stats-vertical-layout">
                        
                        <!-- کل فروش قسطی -->
                        <div class="stat-box-pro bg-primary-light border-primary">
                            <div class="stat-header text-primary-dark">
                                <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="6"/><circle cx="12" cy="12" r="2"/></svg>
                                <span>مبلغ کل فاکتورها (قسطی)</span>
                            </div>
                            <div class="stat-value-container" style="gap: 4px;">
                                <strong class="dir-ltr text-primary-dark">{{ formatNumber(stats.totalSales) }}</strong>
                                <span class="currency-std text-primary-dark">تومان</span>
                            </div>
                        </div>

                        <!-- باکس سود دوتایی یکپارچه با سیستم -->
                        <div class="stat-box-pro bg-success-light border-success p-0" style="overflow: hidden;">
                            <div class="d-flex w-100 align-center">
                                <div class="flex-1 text-center p-md" style="border-left: 1px dashed #a7f3d0;">
                                    <div class="stat-header text-success-dark justify-center mb-xs">
                                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2v20M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/></svg>
                                        <span style="font-size: 11px;">سود قطعی (نقد شده)</span>
                                    </div>
                                    <div class="stat-value-container text-success-dark mt-xs" style="gap: 4px;">
                                        <strong class="dir-ltr" style="font-size: 20px;">{{ formatNumber(stats.totalRealizedProfit) }}</strong>
                                        <span class="currency-std" style="font-size: 10px;">تومان</span>
                                    </div>
                                </div>
                                <div class="flex-1 text-center p-md">
                                    <div class="stat-header text-success-dark justify-center mb-xs" style="opacity: 0.8;">
                                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>
                                        <span style="font-size: 11px;">سود وصول نشده</span>
                                    </div>
                                    <div class="stat-value-container text-success-dark mt-xs" style="opacity: 0.8; gap: 4px;">
                                        <strong class="dir-ltr" style="font-size: 18px;">{{ formatNumber(stats.totalExpectedProfit - stats.totalRealizedProfit) }}</strong>
                                        <span class="currency-std" style="font-size: 10px;">تومان</span>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- ردیف معوقه و جاری -->
                        <div class="stats-horizontal-row">
                            <div class="stat-box-small bg-danger-light border-danger">
                                <div class="stat-header text-danger-dark">
                                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><line x1="15" y1="9" x2="9" y2="15"/><line x1="9" y1="9" x2="15" y2="15"/></svg>
                                    <span>اقساط معوقه</span>
                                </div>
                                <div class="stat-value-container" style="gap: 4px;">
                                    <strong class="dir-ltr text-danger-dark" style="font-size: 20px;">{{ formatNumber(stats.totalOverdue) }}</strong>
                                    <span class="currency-std text-danger-dark" style="font-size: 11px;">تومان</span>
                                </div>
                            </div>
                            
                            <div class="stat-box-small bg-warning-light border-warning">
                                <div class="stat-header text-warning-dark">
                                    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="5" width="20" height="14" rx="2"/><line x1="2" y1="10" x2="22" y2="10"/></svg>
                                    <span>اقساط جاری</span>
                                </div>
                                <div class="stat-value-container" style="gap: 4px;">
                                    <strong class="dir-ltr text-warning-dark" style="font-size: 20px;">{{ formatNumber(stats.totalRemaining) }}</strong>
                                    <span class="currency-std text-warning-dark" style="font-size: 11px;">تومان</span>
                                </div>
                                <span class="stat-sub text-warning-dark opacity-80" style="font-size: 10px; margin-top: 6px; display: block; text-align: center;">({{ stats.activeCount }} پرونده باز)</span>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </div>

        <!-- پاپ آپ اصلی جزئیات پرونده اقساط -->
        <div v-if="showDetailsModal" class="modal-overlay glass-overlay" @click.self="showDetailsModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>

                <div class="modern-sheet-body hide-scroll custom-scroll sheet-body-padded">
                    
                    <div class="modal-profile-header-new">
                        <div class="mph-top-row">
                            <div class="mph-meta-group">
                                <span class="mph-invoice-badge">فاکتور: <span class="dir-ltr d-inline-block">{{ activeInstallment?.invoiceNumber }}</span></span>
                                <span class="mph-date"><span class="dir-ltr d-inline-block">{{ activeInstallment?.date }}</span></span>
                            </div>
                            <button class="btn-circular-close" @click="showDetailsModal = false">
                                <svg viewBox="0 0 24 24" width="18" height="18" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" fill="none"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                            </button>
                        </div>
                        
                        <div class="mph-customer-row">
                            <div class="mph-avatar">{{ activeInstallment?.customerName?.charAt(0) || 'م' }}</div>
                            <div class="mph-customer-details">
                                <h3 class="mph-name">{{ activeInstallment?.customerName || 'مشتری عمومی' }}</h3>
                                <span v-if="activeInstallment?.phone" class="mph-phone dir-ltr">
                                    <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path></svg>
                                    {{ activeInstallment?.phone }}
                                </span>
                            </div>
                        </div>

                        <div class="mph-item-box">
                            <strong class="mph-item-name">{{ activeInstallment?.itemName }}</strong>
                        </div>
                    </div>

                    <div class="hero-balance-section" style="margin-bottom: 24px;" v-if="getFinancials(activeInstallment).debt > 0">
                        <span class="hero-label">مانده اقساط</span>
                        <div class="d-flex align-center justify-center gap-sm mt-xs text-danger-dark" style="direction: rtl;">
                            <strong class="hero-amount dir-ltr">{{ formatNumber(getFinancials(activeInstallment).debt) }}</strong>
                            <span class="hero-currency">تومان</span>
                        </div>
                    </div>
                    <div class="hero-balance-section" style="margin-bottom: 24px;" v-else>
                        <span class="hero-label">وضعیت پرونده</span>
                        <div class="hero-amount-container text-success-dark"><strong class="hero-amount" style="font-size: 26px;">تسویه کامل</strong></div>
                    </div>

                    <div class="section-title-mod" style="color: #6d28d9; margin-top: 8px;">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="5" width="20" height="14" rx="2"></rect><line x1="2" y1="10" x2="22" y2="10"></line></svg>
                        اطلاعات مالی اقساط
                    </div>
                    
                    <div class="solid-stats-grid mb-md" style="border-color: #ddd6fe; box-shadow: 0 4px 10px rgba(109, 40, 217, 0.05);">
                        <div class="ss-item ss-border-b ss-border-l">
                            <span class="ss-lbl">مبلغ کل فاکتور</span>
                            <strong class="ss-val text-dark dir-ltr">{{ formatNumber(getFinancials(activeInstallment).finalPrice) }}</strong>
                        </div>
                        <div class="ss-item ss-border-b ss-border-l">
                            <span class="ss-lbl">تعداد اقساط</span>
                            <strong class="ss-val">{{ activeInstallment?.installCount }} ماهه</strong>
                        </div>
                        <div class="ss-item ss-border-b">
                            <span class="ss-lbl">درصد ماهانه</span>
                            <strong class="ss-val dir-ltr">{{ activeInstallment?.rate || 0 }}٪</strong>
                        </div>
                        
                        <div class="ss-item bg-lightest ss-border-l">
                            <span class="ss-lbl">مبلغ هر قسط</span>
                            <strong class="ss-val text-primary-dark dir-ltr">{{ formatNumber(getFinancials(activeInstallment).paymentPerInst) }}</strong>
                        </div>
                        <div class="ss-item bg-lightest ss-border-l">
                            <span class="ss-lbl">دریافتی علی‌الحساب</span>
                            <strong class="ss-val dir-ltr">{{ formatNumber(getFinancials(activeInstallment).downPayment) }}</strong>
                        </div>
                        <div class="ss-item bg-lightest">
                            <span class="ss-lbl">وصولی اقساط</span>
                            <strong class="ss-val text-success-dark dir-ltr">{{ formatNumber(getFinancials(activeInstallment).instPaid) }}</strong>
                        </div>
                    </div>

                    <button class="btn-guaranty-toggle-centered mb-lg mt-md" :class="{'has-guaranty': activeInstallment?.guaranty?.name || activeInstallment?.guaranty?.chequeNumber}" @click="handleGuarantyClick">
                        <div class="icon-wrapper-centered mb-sm">
                            <svg width="26" height="26" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"></path></svg>
                        </div>
                        <strong class="d-block text-dark mb-xs" style="font-size: 15px;">ضمانت و وثیقه پرونده</strong>
                        
                        <span class="badge-status-guaranty has-data" v-if="activeInstallment?.guaranty?.name || activeInstallment?.guaranty?.chequeNumber">
                            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
                            اطلاعات ثبت شده (مشاهده)
                        </span>
                        <span class="badge-status-guaranty no-data" v-else>
                            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>
                            بدون ضمانت (برای ثبت کلیک کنید)
                        </span>
                    </button>

                    <div class="due-dates-section mb-lg mt-md" v-if="activeInstallment?.installCount > 0" @click="activeTooltipId = null">
                        <span class="dd-title">
                            <svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect><line x1="16" y1="2" x2="16" y2="6"></line><line x1="8" y1="2" x2="8" y2="6"></line><line x1="3" y1="10" x2="21" y2="10"></line></svg>
                            سررسید اقساط
                        </span>
                        
                        <div class="dd-scroll-container hide-scroll">
                            <div v-for="(inst, idx) in generatedDueDates(activeInstallment)" :key="idx" 
                                 class="dd-chip" :class="inst.status"
                                 @click.stop="toggleTooltip(idx, inst.remainingToClear)"
                                 style="justify-content: center; min-width: 105px; padding: 8px 16px;">
                                
                                <div class="d-flex align-center justify-center gap-sm w-100">
                                    <span class="dd-date dir-ltr text-center font-900" style="font-size: 13px;">{{ inst.date }}</span>
                                    <svg v-if="inst.status === 'paid'" class="dd-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
                                    <svg v-else-if="inst.status === 'overdue'" class="dd-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>
                                    <svg v-else class="dd-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>
                                </div>
                                
                                <div v-if="activeTooltipId === idx && inst.remainingToClear > 0" class="dd-rem-tooltip">
                                    نیاز به <strong class="dir-ltr">{{ formatNumber(inst.remainingToClear) }}</strong>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="quick-pay-box mb-md" v-if="getFinancials(activeInstallment).debt > 0">
                        <div class="d-flex align-center gap-sm" dir="rtl">
                            <div class="form-group mb-0 flex-1">
                                <input type="text" :value="formatNumber(quickPayAmount)" @input="handleQuickPayInput" class="quick-pay-input" placeholder="مبلغ پرداختی (تومان)" maxlength="15">
                            </div>
                            <button class="btn-quick-pay" @click="addPayment" :disabled="!quickPayAmount">
                                <svg viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
                            </button>
                        </div>
                    </div>

                    <div class="payments-timeline-wrapper" v-if="activeInstallment?.payments && activeInstallment.payments.length > 0">
                        <div class="timeline-header">
                            <div class="th-icon">
                                <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline></svg>
                            </div>
                            <h4 class="th-title">تاریخچه واریزی‌ها</h4>
                            <div class="th-badge">{{ activeInstallment.payments.length }} پرداختی</div>
                        </div>
                        
                        <div class="timeline-list">
                            <div v-for="(p, index) in activeInstallment.payments" :key="p.id" class="tl-item">
                                <div class="tl-marker"><span>{{ index + 1 }}</span></div>
                                <div class="tl-content">
                                    <div class="tl-amount-group d-flex align-center gap-xs" style="direction: rtl;">
                                        <strong class="tl-amount dir-ltr text-dark">{{ formatNumber(p.amount) }}</strong>
                                        <span class="tl-currency text-muted">تومان</span>
                                    </div>
                                    
                                    <div class="tl-date-group">
                                        <span class="tl-date dir-ltr">{{ p.date }}</span>
                                        <button class="tl-del-btn" @click="deletePayment(p.id)" title="حذف">
                                            <svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </div>

        <!-- پاپ‌آپ مشاهده جزئیات ضمانت -->
        <div v-if="showGuarantyViewModal" class="modal-overlay glass-overlay" style="z-index: 10010;" @click.self="showGuarantyViewModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>
                
                <div class="modal-top-bar px-md mb-md">
                    <div class="d-flex align-center gap-sm">
                        <div style="width:46px; height:46px; border-radius:14px; background: linear-gradient(135deg, #14b8a6, #0f766e); color: white; display:flex; align-items:center; justify-content:center; flex-shrink: 0; box-shadow: 0 4px 10px rgba(15, 118, 110, 0.25);">
                            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"></path></svg>
                        </div>
                        <div>
                            <h3 class="m-0 text-dark" style="font-size: 17px; font-weight: 900;">جزئیات ضمانت و چک</h3>
                            <span class="text-muted" style="font-size: 12px; font-weight: 700;">اطلاعات ضامن پرونده</span>
                        </div>
                    </div>
                    <button class="btn-circular-close bg-light" @click="showGuarantyViewModal = false">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                    </button>
                </div>
                
                <div class="modern-sheet-body hide-scroll px-md pb-xl">
                    <div class="guaranty-card-premium mb-md">
                        <div class="g-card-header pb-sm mb-sm border-bottom-dashed">
                            <div class="d-flex align-center gap-xs">
                                <div style="width: 8px; height: 8px; background: #0f766e; border-radius: 50%;"></div>
                                <span class="g-card-title text-dark">جزئیات ثبت شده</span>
                            </div>
                            <span class="text-xs text-muted dir-ltr">{{ activeInstallment?.guaranty?.date || 'بدون تاریخ' }}</span>
                        </div>

                        <div class="g-info-grid">
                            <div class="g-info-item" v-if="activeInstallment?.guaranty?.name">
                                <span class="g-lbl"><svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg> نام ضامن</span>
                                <strong class="g-val">{{ activeInstallment.guaranty.name }}</strong>
                            </div>
                            <div class="g-info-item" v-if="activeInstallment?.guaranty?.phone">
                                <span class="g-lbl"><svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path></svg> شماره تماس</span>
                                <strong class="g-val dir-ltr text-left">{{ activeInstallment.guaranty.phone }}</strong>
                            </div>
                            <div class="g-info-item" v-if="activeInstallment?.guaranty?.bank">
                                <span class="g-lbl"><svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect><line x1="16" y1="2" x2="16" y2="6"></line><line x1="8" y1="2" x2="8" y2="6"></line><line x1="3" y1="10" x2="21" y2="10"></line></svg> بانک صادرکننده</span>
                                <strong class="g-val">{{ activeInstallment.guaranty.bank }}</strong>
                            </div>
                            <div class="g-info-item" v-if="activeInstallment?.guaranty?.chequeNumber">
                                <span class="g-lbl"><svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect><line x1="16" y1="2" x2="16" y2="6"></line><line x1="8" y1="2" x2="8" y2="6"></line><line x1="3" y1="10" x2="21" y2="10"></line></svg> شماره چک/صیاد</span>
                                <strong class="g-val dir-ltr text-left">{{ activeInstallment.guaranty.chequeNumber }}</strong>
                            </div>
                            
                            <div class="g-info-item" v-if="activeInstallment?.guaranty?.amount">
                                <span class="g-lbl"><svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 12 20 22 4 22 4 12"></polyline><rect x="2" y="7" width="20" height="5"></rect><line x1="12" y1="22" x2="12" y2="7"></line></svg> مبلغ ضمانت</span>
                                <div class="d-flex align-center justify-end gap-xs mt-xs" style="direction: rtl;">
                                    <strong class="g-val dir-ltr text-dark">{{ formatNumber(activeInstallment.guaranty.amount) }}</strong>
                                    <span style="font-size: 10px; font-weight: 700; color: #64748b;">تومان</span>
                                </div>
                            </div>
                            
                        </div>

                        <div class="g-info-item full-width mt-sm" v-if="activeInstallment?.guaranty?.description" style="background: #fffbeb; border-color: #fde68a;">
                            <span class="g-lbl" style="color: #b45309;"><svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg> توضیحات تکمیلی</span>
                            <strong class="g-val" style="line-height: 1.6; font-size: 11.5px; color: #78350f;">{{ activeInstallment.guaranty.description }}</strong>
                        </div>
                    </div>

                    <div class="d-flex gap-sm mt-sm">
                        <button class="btn btn-primary flex-1 m-0" style="background: #f1f5f9; color: #0f766e; border-radius: 12px; box-shadow: none;" @click="showGuarantyViewModal = false; openGuarantyForm();">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="margin-left: 6px;"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"></path><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"></path></svg>
                            ویرایش اطلاعات
                        </button>
                        <button class="btn btn-danger flex-1 m-0" style="background: #fff1f2; color: #e11d48; border-radius: 12px; box-shadow: none;" @click="deleteGuaranty">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="margin-left: 6px;"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path></svg>
                            حذف ضمانت
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- پاپ‌آپ فرم ثبت/ویرایش ضمانت -->
        <div v-if="showGuarantyModal" class="modal-overlay glass-overlay" style="z-index: 10020;" @click.self="showGuarantyModal = false">
            <div class="modern-bottom-sheet">
                <div class="sheet-drag-handle"></div>
                <div class="modal-top-bar px-md mb-md">
                    <div class="d-flex align-center gap-sm">
                        <div style="width:46px; height:46px; border-radius:14px; background:#ccfbf1; color:#0f766e; display:flex; align-items:center; justify-content:center; flex-shrink: 0;">
                            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"></path></svg>
                        </div>
                        <div>
                            <h3 class="m-0 text-dark" style="font-size: 17px; font-weight: 900;">اطلاعات ضمانت</h3>
                            <span class="text-muted" style="font-size: 12px; font-weight: 700;">ثبت مشخصات ضامن و چک</span>
                        </div>
                    </div>
                    <button class="btn-circular-close bg-light" @click="showGuarantyModal = false">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
                    </button>
                </div>
                
                <div class="modern-sheet-body hide-scroll px-md pb-xl">
                    <div class="form-row d-flex gap-sm mb-sm flex-mobile-col">
                        <div class="form-group flex-1 m-0">
                            <label class="text-xs font-800 text-muted mb-xs d-block">نام ضامن / صاحب چک:</label>
                            <input type="text" v-model="guarantyForm.name" class="form-control" placeholder="نام کامل">
                        </div>
                        <div class="form-group flex-1 m-0">
                            <label class="text-xs font-800 text-muted mb-xs d-block">شماره تماس ضامن:</label>
                            <input type="tel" v-model="guarantyForm.phone" class="form-control dir-ltr text-center" placeholder="09...">
                        </div>
                    </div>

                    <div class="form-row d-flex gap-sm mb-sm flex-mobile-col">
                        <div class="form-group flex-1 m-0">
                            <label class="text-xs font-800 text-muted mb-xs d-block">بانک صادرکننده چک:</label>
                            <input type="text" v-model="guarantyForm.bank" class="form-control" placeholder="مثال: ملی">
                        </div>
                        <div class="form-group flex-1 m-0">
                            <label class="text-xs font-800 text-muted mb-xs d-block">شماره چک / صیادی:</label>
                            <input type="text" v-model="guarantyForm.chequeNumber" class="form-control dir-ltr text-center" placeholder="شماره">
                        </div>
                    </div>

                    <div class="form-row d-flex gap-sm mb-sm flex-mobile-col">
                        <div class="form-group flex-1 m-0">
                            <label class="text-xs font-800 text-muted mb-xs d-block">تاریخ چک:</label>
                            <input type="text" v-model="guarantyForm.date" class="form-control dir-ltr text-center" placeholder="1402/05/10">
                        </div>
                        <div class="form-group flex-1 m-0">
                            <label class="text-xs font-800 text-muted mb-xs d-block">مبلغ چک (تومان):</label>
                            <input type="text" :value="formatNumber(guarantyForm.amount)" @input="handleGuarantyAmountInput" class="form-control dir-ltr text-center font-900" placeholder="0">
                        </div>
                    </div>

                    <div class="form-group mb-xl">
                        <label class="text-xs font-800 text-muted mb-xs d-block">توضیحات تکمیلی:</label>
                        <input type="text" v-model="guarantyForm.description" class="form-control" placeholder="در صورت نیاز...">
                    </div>

                    <div class="d-flex gap-sm">
                        <button class="btn btn-primary flex-2 m-0" style="background: #0f766e; border-radius: 14px; box-shadow: 0 4px 12px rgba(15, 118, 110, 0.25);" @click="saveGuaranty">ذخیره اطلاعات ضمانت</button>
                        <button class="btn btn-secondary flex-1 m-0" style="border-radius: 14px;" @click="showGuarantyModal = false">انصراف</button>
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
        const { formatNumber, unformatNumber, getFinancials, getPersianDate, addMonthsPersian, getPersianDateInt, toEnglishDigits } = window.SysUtils;

        const installments = ref([]);
        const searchInput = ref('');
        const searchQuery = ref('');
        const showStatsModal = ref(false);
        const showDetailsModal = ref(false);
        const showGuarantyViewModal = ref(false); 
        const showGuarantyModal = ref(false); 
        const activeInstallment = ref(null);
        
        const quickPayAmount = ref('');
        const activeTooltipId = ref(null);
        let debounceTimer;

        const guarantyForm = reactive({
            name: '', phone: '', chequeNumber: '', bank: '', date: '', amount: '', description: ''
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

        const handleGuarantyAmountInput = (e) => {
            const el = e.target;
            const cursorPos = el.selectionStart;
            const oldLen = el.value.length;
            
            guarantyForm.amount = unformatNumber(el.value);
            
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
                const allSales = await window.DB.getAll('sales');
                installments.value = allSales.filter(s => s.paymentType === 'installment');
                installments.value.sort((a, b) => b.timestamp - a.timestamp);
                
                if (activeInstallment.value) {
                    const updatedInst = installments.value.find(s => s.id === activeInstallment.value.id);
                    if (updatedInst) activeInstallment.value = updatedInst;
                }
            }
        };

        const generatedDueDates = (sale) => {
            if (!sale || !sale.installCount) return [];
            const count = sale.installCount;
            const interval = Number(sale.installInterval) || 1;
            const fin = getFinancials(sale);
            
            const instPaid = fin.instPaid; 
            const todayInt = getPersianDateInt(getPersianDate());
            
            const dates = [];
            let currentExpected = addMonthsPersian(sale.date, interval);
            
            for (let i = 0; i < count; i++) {
                let needed = (i + 1) * fin.paymentPerInst;
                if (i === count - 1) needed = fin.totalInstDebt;
                
                const isPaid = instPaid >= (needed - 100); 
                const dInt = getPersianDateInt(currentExpected);
                
                let status = 'pending';
                if (isPaid) {
                    status = 'paid';
                } else if (todayInt > dInt) {
                    status = 'overdue';
                }

                const remainingToClear = isPaid ? 0 : Math.max(0, needed - instPaid);
                
                dates.push({ date: currentExpected, status, remainingToClear });
                currentExpected = addMonthsPersian(currentExpected, interval);
            }
            return dates;
        };

        const processedInstallments = computed(() => {
            let list = installments.value;
            if (searchQuery.value) {
                const q = searchQuery.value.toLowerCase();
                list = list.filter(inst => 
                    (inst.customerName && inst.customerName.toLowerCase().includes(q)) ||
                    (inst.itemName && inst.itemName.toLowerCase().includes(q)) ||
                    (inst.invoiceNumber && inst.invoiceNumber.toLowerCase().includes(q))
                );
            }
            
            return list.map(inst => {
                const fin = getFinancials(inst);
                
                let progress = 100;
                if (fin.finalPrice > 0) {
                    progress = (fin.paid / fin.finalPrice) * 100;
                    if (progress > 100) progress = 100;
                }
                
                const dates = generatedDueDates(inst);
                const hasOverdue = dates.some(d => d.status === 'overdue');

                return {
                    ...inst,
                    _debt: fin.debt,
                    _paid: fin.paid,
                    _hasOverdue: hasOverdue,
                    _progress: progress
                };
            });
        });

        const toggleTooltip = (idx, remaining) => {
            if (remaining > 0) {
                if (activeTooltipId.value === idx) {
                    activeTooltipId.value = null;
                } else {
                    activeTooltipId.value = idx;
                }
            } else {
                activeTooltipId.value = null;
            }
        };

        const stats = computed(() => {
            let totalSales = 0, totalPaid = 0, activeCount = 0, totalOverdue = 0;
            let totalExpectedProfit = 0, totalRealizedProfit = 0, totalDebt = 0;

            installments.value.forEach(inst => {
                const fin = getFinancials(inst);
                totalSales += fin.finalPrice;
                totalPaid += fin.paid;
                totalDebt += fin.debt;
                totalExpectedProfit += fin.expectedProfit;
                totalRealizedProfit += fin.realizedProfit;
                
                if (fin.debt > 0) {
                    activeCount++;
                    const dates = generatedDueDates(inst);
                    dates.forEach(d => {
                        if (d.status === 'overdue') {
                            totalOverdue += d.remainingToClear;
                        }
                    });
                }
            });
            return { totalSales, totalPaid, totalDebt, activeCount, totalOverdue, totalExpectedProfit, totalRealizedProfit, totalCount: installments.value.length };
        });

        const openDetails = (inst) => {
            activeInstallment.value = inst;
            quickPayAmount.value = ''; 
            activeTooltipId.value = null; 
            showGuarantyViewModal.value = false;
            showDetailsModal.value = true;
        };

        const handleGuarantyClick = () => {
            if (activeInstallment.value?.guaranty?.name || activeInstallment.value?.guaranty?.chequeNumber) {
                showGuarantyViewModal.value = true;
            } else {
                openGuarantyForm();
            }
        };

        const openGuarantyForm = () => {
            const g = activeInstallment.value.guaranty || {};
            Object.assign(guarantyForm, {
                name: g.name || '',
                phone: g.phone || '',
                chequeNumber: g.chequeNumber || '',
                bank: g.bank || '',
                date: g.date || '',
                amount: g.amount || '',
                description: g.description || ''
            });
            showGuarantyModal.value = true;
        };

        const saveGuaranty = async () => {
            const sale = JSON.parse(JSON.stringify(activeInstallment.value));
            sale.guaranty = {
                name: guarantyForm.name,
                phone: toEnglishDigits(guarantyForm.phone),
                chequeNumber: toEnglishDigits(guarantyForm.chequeNumber),
                bank: guarantyForm.bank,
                date: toEnglishDigits(guarantyForm.date),
                amount: guarantyForm.amount,
                description: guarantyForm.description
            };
            
            await window.DB.put('sales', sale);
            activeInstallment.value = sale;
            showGuarantyModal.value = false;
            if(!showGuarantyViewModal.value) showGuarantyViewModal.value = true;
            await loadData();
        };

        const deleteGuaranty = async () => {
            if (!confirm('آیا از حذف اطلاعات ضمانت اطمینان دارید؟')) return;
            const sale = JSON.parse(JSON.stringify(activeInstallment.value));
            sale.guaranty = null;
            
            await window.DB.put('sales', sale);
            activeInstallment.value = sale;
            showGuarantyViewModal.value = false;
            await loadData();
        };

        const addPayment = async () => {
            const amt = Number(quickPayAmount.value);
            if (amt <= 0) return alert('مبلغ نامعتبر است');
            
            const currentDebt = getFinancials(activeInstallment.value).debt;
            if (amt > currentDebt) return alert('مبلغ پرداختی بیشتر از مانده اقساط است.');

            const sale = JSON.parse(JSON.stringify(activeInstallment.value));
            if (!sale.payments) sale.payments = [];
            
            sale.payments.push({
                id: Date.now(),
                amount: amt,
                date: getPersianDate()
            });
            
            await window.DB.put('sales', sale);
            quickPayAmount.value = '';
            await loadData();
        };

        const deletePayment = async (pid) => {
            if (!confirm('آیا از حذف این پرداختی اطمینان دارید؟')) return;
            const sale = JSON.parse(JSON.stringify(activeInstallment.value));
            sale.payments = sale.payments.filter(p => p.id !== pid);
            
            await window.DB.put('sales', sale);
            await loadData();
        };

        onMounted(() => { loadData(); });
        onActivated(() => { loadData(); });

        return {
            installments, searchInput, searchQuery, onSearchInput, clearSearch, processedInstallments, stats,
            showStatsModal, showDetailsModal, showGuarantyModal, showGuarantyViewModal, activeInstallment, handleQuickPayInput, handleGuarantyAmountInput,
            quickPayAmount, formatNumber, unformatNumber, generatedDueDates,
            activeTooltipId, toggleTooltip,
            openDetails, getFinancials,
            addPayment, deletePayment,
            guarantyForm, handleGuarantyClick, openGuarantyForm, saveGuaranty, deleteGuaranty
        };
    }
}
</script>


<style scoped>
.stat-value-container { display: flex; align-items: center; justify-content: center; gap: 4px; direction: rtl; margin-top: 6px; width: 100%; }
.stat-value-container strong { font-size: 24px; font-weight: 900; letter-spacing: -0.5px; line-height: 1; margin: 0; padding: 0; white-space: nowrap; }
.stat-value-container .currency-std { font-size: 11px; font-weight: 800; opacity: 0.9; margin: 0; padding: 0; white-space: nowrap; }

.modern-bottom-sheet { background: #ffffff; width: 100%; max-width: 600px; border-radius: 36px 36px 0 0; padding: 20px 12px 30px 12px; animation: slideUpModern 0.4s cubic-bezier(0.2, 0.8, 0.2, 1); display: flex; flex-direction: column; max-height: 92vh; box-shadow: 0 -10px 40px rgba(0,0,0,0.1); position: relative; }
@keyframes slideUpModern { from { transform: translateY(100%); } to { transform: translateY(0); } }
.sheet-drag-handle { width: 48px; height: 5px; background: #e5e7eb; border-radius: 4px; margin: 0 auto 20px auto; flex-shrink: 0; }
.hide-scroll::-webkit-scrollbar { display: none; }
.modal-overlay.glass-overlay { align-items: flex-end; padding: 0; background: rgba(17, 24, 39, 0.45); backdrop-filter: blur(6px); display: flex; justify-content: center; z-index: 10000; position: fixed !important; inset: 0 !important; }

.px-xs { padding-left: 8px; padding-right: 8px; }
.px-md { padding-left: 12px; padding-right: 12px; }
.pb-xl { padding-bottom: 32px; }
.text-center { text-align: center !important; }
.modal-top-bar-modern { display: flex; align-items: center; justify-content: space-between; width: 100%; flex-shrink: 0; margin-bottom: 24px; direction: rtl; }
.icon-box-modern { width: 48px; height: 48px; border-radius: 16px; background: #eff6ff; color: #2563eb; display: flex; align-items: center; justify-content: center; flex-shrink: 0; margin-right: 14px; }
.title-box-modern { text-align: right; flex: 1; margin-right: 14px; }
.title-box-modern .m-0 { font-size: 16px; font-weight: 900; }
.title-box-modern .text-muted { font-size: 11.5px; font-weight: 700; color: #6b7280; }
.btn-circular-close-modern { width: 40px; height: 40px; border-radius: 50%; background: #f1f5f9; color: #475569; border: none; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; }
.btn-circular-close-modern:active { transform: scale(0.9); background: #e2e8f0; }
.modal-top-bar { display: flex; justify-content: space-between; align-items: center; width: 100%; flex-shrink: 0; margin-bottom: 20px; }
.btn-circular-close { width: 36px; height: 36px; border-radius: 50%; background: #f3f4f6; border: none; color: #4b5563; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; flex-shrink: 0; margin-right: auto; }
.btn-circular-close:active { transform: scale(0.9); background: #e2e8f0; }
.bg-light { background: #f1f5f9; color: #475569; }

/* اصلاح فضاهای خالی کارت‌های آمار و وسط‌چین کردن کامل */
.stats-vertical-layout { display: flex; flex-direction: column; gap: 14px; width: 100%; }
.stats-horizontal-row { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; width: 100%; direction: rtl; }
.flex-column-safe { flex-direction: column !important; }
.align-center-safe { align-items: center !important; justify-content: center !important; text-align: center; }
.justify-center { justify-content: center !important; }

.stat-box-pro { border-radius: 20px; padding: 16px 12px; border: 1px solid transparent; display: flex; flex-direction: column; align-items: center; text-align: center; overflow: hidden; min-width: 0; box-shadow: 0 4px 12px rgba(0,0,0,0.03); direction: rtl; }
.stat-box-small { border-radius: 20px; padding: 14px 8px; border: 1px solid transparent; display: flex; flex-direction: column; align-items: center; text-align: center; overflow: hidden; min-width: 0; box-shadow: 0 4px 12px rgba(0,0,0,0.03); direction: rtl; }
.stat-header { display: flex; align-items: center; justify-content: center; gap: 8px; font-size: 13px; font-weight: 900; margin-bottom: 8px; width: 100%; text-align: center; white-space: nowrap; }
.stat-box-small .stat-header { justify-content: center; margin-bottom: 6px; font-size: 11.5px; }

.bg-primary-light { background: #eff6ff; } .border-primary { border-color: #bfdbfe; } .text-primary-dark { color: #1e40af; }
.bg-success-light { background: #f0fdf4; } .border-success { border-color: #bbf7d0; } .text-success-dark { color: #166534; }
.bg-danger-light { background: #fef2f2; } .border-danger { border-color: #fecaca; } .text-danger-dark { color: #991b1b; }
.bg-warning-light { background: #fffbeb; } .border-warning { border-color: #fde68a; } .text-warning-dark { color: #92400e; }
.bg-slate-light { background: #f8fafc; } .border-slate { border-color: #e2e8f0; } .text-slate-dark { color: #0f172a; }
.relative-page { padding-bottom: 120px; }
.dir-ltr { direction: ltr; display: inline-block; }
.gap-sm { gap: 8px; }
.gap-xs { gap: 4px; }
.mt-xl { margin-top: 32px !important; }
.currency-label { font-size: 11px; font-weight: 800; margin-top: 2px; }
.top-action-bar { display: flex; gap: 8px; width: 100%; align-items: stretch; margin-bottom: 16px; }
.stats-banner-card { flex: 1; background: linear-gradient(135deg, #3b82f6, #2563eb); border-radius: 16px; padding: 14px 16px; display: flex; align-items: center; justify-content: space-between; cursor: pointer; color: white; box-shadow: 0 6px 15px rgba(37, 99, 235, 0.25); transition: all 0.3s ease; }
.stats-banner-card:active { transform: scale(0.97); }
.sbc-icon { background: rgba(255,255,255,0.2); width: 38px; height: 38px; border-radius: 12px; display: flex; align-items: center; justify-content: center; }
.sbc-text { display: flex; flex-direction: column; }
.sbc-title { font-size: 14px; font-weight: 900; }
.sbc-subtitle { font-size: 10px; opacity: 0.9; }
.sbc-arrow { background: rgba(255,255,255,0.15); width: 28px; height: 28px; border-radius: 50%; display: flex; align-items: center; justify-content: center; }
.app-search-wrapper { width: 100%; margin-bottom: 16px; }
.app-search-inner { position: relative; display: flex; align-items: center; background: #ffffff; border-radius: 24px; box-shadow: 0 4px 16px rgba(0,0,0,0.03); border: 1px solid #f3f4f6; }
.app-search-icon { position: absolute; right: 16px; width: 18px; color: #9ca3af; }
.app-search-input { width: 100%; border: none; background: transparent; padding: 14px 44px 14px 16px; font-size: 13px; font-weight: 700; outline: none; }
.app-search-clear { position: absolute; left: 12px; background: #f3f4f6; color: #6b7280; border: none; border-radius: 50%; width: 24px; height: 24px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
.empty-state-app { text-align: center; padding: 40px 20px; color: #9ca3af; }
.empty-icon { font-size: 40px; margin-bottom: 12px; filter: grayscale(1); opacity: 0.5; }

/* ------ کارت اقساط (طراحی جدید) ------ */
.app-list-wrapper { display: flex; flex-direction: column; gap: 14px; margin-bottom: 20px; }
.app-card { background: #ffffff; border-radius: 22px; padding: 22px 16px; box-shadow: 0 6px 20px rgba(0,0,0,0.025); transition: transform 0.2s ease, box-shadow 0.2s; border: 1px solid transparent; border-right: 4px solid transparent; display: flex; flex-direction: column; justify-content: space-between; }
.app-card.clickable:active { transform: scale(0.98); border-color: var(--primary-light); }
.app-card.overdue-highlight { border-right: 4px solid #ef4444; background: linear-gradient(270deg, #fffbfb 0%, #ffffff 100%); box-shadow: 0 4px 15px rgba(239, 68, 68, 0.15); }
.app-card.settled-highlight { border-right: 4px solid #10b981; background: linear-gradient(270deg, #ecfdf5 0%, #ffffff 100%); box-shadow: 0 4px 15px rgba(16, 185, 129, 0.1); }

.sc-header-modern { display: flex; justify-content: space-between; align-items: flex-start; gap: 8px; margin-bottom: 20px; width: 100%; }
.sc-right-info { display: flex; flex-direction: column; align-items: flex-start; text-align: right; flex: 1; min-width: 0; }
.sc-left-badges { display: flex; flex-direction: column; align-items: flex-end; gap: 8px; flex-shrink: 0; }

.sc-meta-row { display: flex; align-items: center; gap: 12px; margin-bottom: 8px; width: 100%; }
.sc-invoice-badge { font-size: 11px; font-weight: 800; background: #f1f5f9; color: #475569; padding: 4px 10px; border-radius: 8px; border: 1px solid #e2e8f0; }
.sc-date-text { font-size: 11px; font-weight: 800; color: #94a3b8; }
.sc-name-text { font-size: 17px; font-weight: 900; color: #0f172a; margin-bottom: 6px; line-height: 1.4; display: block; }
.sc-item-box { font-size: 12.5px; font-weight: 900; color: var(--primary-dark); background: var(--primary-light); padding: 5px 12px; border-radius: 8px; display: inline-block; align-self: flex-start; border: 1px dashed #bfdbfe; }

.sc-badge-type { font-size: 10px; font-weight: 800; padding: 4px 10px; border-radius: 8px; text-align: center; }
.type-installment { background: var(--purple-light); color: var(--purple); border: 1px solid #ddd6fe; }

.sc-badge-status { font-size: 10.5px; font-weight: 800; padding: 4px 10px; border-radius: 8px; display: flex; align-items: center; gap: 4px; text-align: center; }
.status-pending { background: #fef3c7; color: #b45309; border: 1px solid #fde68a; }
.status-settled { background: #d1fae5; color: #047857; }
@keyframes pulseAlert { 0% { opacity: 1; transform: scale(1); } 50% { opacity: 0.6; transform: scale(1.1); } 100% { opacity: 1; transform: scale(1); } }
.status-debt { background: #fee2e2; color: #dc2626; border: 1px solid #fca5a5; box-shadow: 0 2px 6px rgba(239,68,68,0.2); }
.status-debt svg { animation: pulseAlert 1.5s infinite; }

.app-progress-section { width: 100%; margin-bottom: 4px; }
.app-progress-labels { display: flex; justify-content: space-between; font-size: 11px; margin-bottom: 6px; }
.app-progress-label { color: #6b7280; font-weight: 800; }
.app-progress-percent { color: #1f2937; font-weight: 900; }
.app-progress-track { width: 100%; height: 6px; background: #f3f4f6; border-radius: 10px; overflow: hidden; }
.app-progress-fill { height: 100%; border-radius: 10px; transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1); }
.fill-primary { background: linear-gradient(90deg, #818cf8, #3b82f6); }
.fill-success { background: linear-gradient(90deg, #34d399, #10b981); }

/* Grid متقارن فوتر */
.sale-card-footer-grid { display: grid; grid-template-columns: 1fr 1px 1fr; align-items: center; background: #f8fafc; padding: 14px 10px; border-radius: 16px; width: 100%; border: 1px solid #f1f5f9; }
.sale-footer-col { display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 4px; text-align: center; }
.sale-footer-divider { width: 1px; height: 30px; background: #e2e8f0; margin: 0 auto; }
.app-footer-label { font-size: 10px; color: #9ca3af; font-weight: 700; margin: 0; }
.app-footer-value { font-size: 16px; font-weight: 900; line-height: 1; }

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
    background: linear-gradient(135deg, #a78bfa, #818cf8);
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
.quick-pay-input:focus { border-color: var(--primary); background: white; box-shadow: 0 0 0 3px var(--primary-light); }
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
.due-dates-section { width: 100%; display: flex; flex-direction: column; gap: 10px; direction: rtl; }
.dd-title { font-size: 12px; font-weight: 900; color: #475569; display: flex; align-items: center; gap: 6px; }
.dd-scroll-container { display: flex; gap: 8px; overflow-x: auto; padding-top: 45px; margin-top: -35px; padding-bottom: 10px; direction: rtl; scroll-behavior: smooth; }
.dd-chip { display: flex; align-items: center; gap: 6px; padding: 6px 10px; border-radius: 12px; border: 1.5px solid transparent; flex-shrink: 0; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); cursor: pointer; position: relative; }
.dd-badge { width: 22px; height: 22px; border-radius: 6px; display: flex; align-items: center; justify-content: center; font-size: 11px; font-weight: 900; }
.dd-date { font-size: 12px; font-weight: 800; letter-spacing: 0.5px; }
.dd-icon { width: 14px; height: 14px; }
@keyframes popGlow { 0% { box-shadow: 0 0 0 0 rgba(234, 179, 8, 0.7); } 70% { box-shadow: 0 0 0 6px rgba(234, 179, 8, 0); } 100% { box-shadow: 0 0 0 0 rgba(234, 179, 8, 0); } }
.dd-rem-tooltip { position: absolute; bottom: calc(100% + 8px); left: 50%; transform: translateX(-50%); background: #fde047; color: #713f12; padding: 6px 10px; border-radius: 10px; font-size: 11px; font-weight: 900; white-space: nowrap; z-index: 50; pointer-events: none; animation: popGlow 1.5s infinite; }
.dd-rem-tooltip::after { content: ''; position: absolute; top: 100%; left: 50%; transform: translateX(-50%); border-width: 5px; border-style: solid; border-color: #fde047 transparent transparent transparent; }
.dd-chip.pending { background: #f8fafc; border-color: #e2e8f0; color: #64748b; }
.dd-chip.pending .dd-badge { background: #e2e8f0; color: #475569; }
.dd-chip.paid { background: #ecfdf5; border-color: #34d399; color: #059669; }
.dd-chip.paid .dd-badge { background: #d1fae5; color: #047857; }
@keyframes overdueBlink { 0% { background: #fef2f2; border-color: #ef4444; box-shadow: 0 0 0 rgba(239,68,68,0); } 50% { background: #fee2e2; border-color: #f87171; box-shadow: 0 0 8px rgba(239,68,68,0.5); } 100% { background: #fef2f2; border-color: #ef4444; box-shadow: 0 0 0 rgba(239,68,68,0); } }
.dd-chip.overdue { color: #dc2626; animation: overdueBlink 1.5s infinite; }
.dd-chip.overdue .dd-badge { background: #fee2e2; color: #b91c1c; }

.btn-guaranty-toggle-centered { width: 100%; background: #ffffff; border: 1px dashed #cbd5e1; border-radius: 24px; padding: 24px 16px; display: flex; flex-direction: column; align-items: center; justify-content: center; cursor: pointer; transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); box-shadow: 0 4px 15px rgba(15, 23, 42, 0.02); margin-top: 24px; outline: none; }
.btn-guaranty-toggle-centered:active { transform: scale(0.97); background: #f8fafc; }
.btn-guaranty-toggle-centered.has-guaranty { border: 1px solid #14b8a6; background: linear-gradient(180deg, #f0fdfa, #ffffff); box-shadow: 0 6px 20px rgba(20, 184, 166, 0.1); }
.icon-wrapper-centered { width: 60px; height: 60px; background: #f1f5f9; color: #64748b; border-radius: 18px; display: flex; align-items: center; justify-content: center; transition: 0.3s; }
.has-guaranty .icon-wrapper-centered { background: linear-gradient(135deg, #14b8a6, #0f766e); color: #ffffff; box-shadow: 0 6px 15px rgba(15, 118, 110, 0.25); }

.badge-status-guaranty { font-size: 11px; font-weight: 800; padding: 6px 12px; border-radius: 10px; display: inline-flex; align-items: center; gap: 6px; margin-top: 4px; }
.badge-status-guaranty.has-data { background: #d1fae5; color: #047857; border: 1px solid #a7f3d0; }
.badge-status-guaranty.no-data { background: #f1f5f9; color: #64748b; border: 1px solid #e2e8f0; }

.guaranty-card-premium { background: #ffffff; border: 1px solid #e2e8f0; border-radius: 20px; padding: 20px; position: relative; overflow: hidden; }
.guaranty-card-premium::before { content: ''; position: absolute; top: 0; right: 0; bottom: 0; width: 4px; background: linear-gradient(to bottom, #0d9488, #14b8a6); border-radius: 0 8px 8px 0; }
.g-card-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px dashed #e2e8f0; padding-bottom: 12px; }
.g-card-title { font-size: 13.5px; font-weight: 900; color: #0f172a; }

.g-info-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 16px; }
.g-info-item { display: flex; flex-direction: column; gap: 4px; background: #f8fafc; padding: 10px 12px; border-radius: 12px; border: 1px solid #f1f5f9; }
.g-info-item.full-width { grid-column: span 2; }
.g-info-item.highlight-amount { background: linear-gradient(135deg, #14b8a6, #0f766e); border: none; box-shadow: 0 4px 12px rgba(15, 118, 110, 0.2); margin-top: 6px; }
.g-lbl { font-size: 10px; color: #64748b; font-weight: 800; display: flex; align-items: center; gap: 4px; margin-bottom: 2px;}
.g-lbl svg { opacity: 0.7; }
.g-val { font-size: 12.5px; color: #0f172a; font-weight: 900; }
</style>


