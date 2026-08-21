<template>
  <div class="dashboard-page">
    
    <!-- کادر هشدار با قابلیت بسته شدن -->
    <div v-if="showBackupWarning" class="alert-box alert-danger mb-md" style="background: linear-gradient(to right, #fff1f2, #ffe4e6); border-color: #fecdd3; position: relative;">
      <button @click="showBackupWarning = false" style="position: absolute; top: 12px; left: 12px; background: transparent; border: none; cursor: pointer; color: #e11d48; padding: 4px;">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
      </button>

      <div class="d-flex align-center gap-sm">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#e11d48" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></svg>
        <strong style="color: #e11d48; font-size: 14px;">هشدار مهم: حفظ اطلاعات!</strong>
      </div>
      <p style="font-size: 11.5px; color: #9f1239; margin-top: 8px; line-height: 1.6; font-weight: 700; padding-left: 20px;">
        اطلاعات این نرم‌افزار فقط در مرورگر همین دستگاه ذخیره می‌شود. لطفاً هرگز تاریخچه مرورگر (Clear Data/Cache) را پاک نکنید، در غیر این صورت تمام فاکتورها حذف خواهند شد. 
        حتماً به صورت منظم بکاپ بگیرید.
      </p>
      <button @click="backupData" class="btn btn-primary" style="background: #e11d48; margin-top: 10px; font-size: 12px; padding: 10px; border-radius: 12px; box-shadow: 0 4px 12px rgba(225, 29, 72, 0.3);">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="margin-left: 6px;"><polyline points="9 18 15 12 9 6"></polyline></svg>
        تهیه فایل پشتیبان (همین الان)
      </button>
    </div>

    <div class="main-profit-card">
      <div class="profit-orb-1"></div>
      <div class="profit-orb-2"></div>

      <div class="profit-content-wrapper">
        <div class="profit-title">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
            <line x1="12" y1="1" x2="12" y2="23"></line>
            <path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path>
          </svg>
          سود محقق‌شده کل سیستم
        </div>

        <div class="profit-amount-container">
          <span class="profit-amount dir-ltr">{{ formatNumber(dashStats.totalRealized) }}</span>
          <span class="profit-currency">تومان</span>
        </div>
      </div>

      <svg class="profit-svg-bg" viewBox="0 0 500 150" preserveAspectRatio="none">
        <path d="M0,150 Q125,50 250,100 T500,20 L500,150 L0,150 Z" fill="rgba(255, 255, 255, 0.1)"></path>
      </svg>
    </div>

    <div class="dash-filters-container">
      <button class="dash-btn" :class="{ active: currentFilter === 'all' }" @click="setFilter('all')">همه</button>
      <button class="dash-btn" :class="{ active: currentFilter === '3m' }" @click="setFilter('3m')">۳ ماه</button>
      <button class="dash-btn" :class="{ active: currentFilter === '1m' }" @click="setFilter('1m')">۱ ماه</button>
      <button class="dash-btn" :class="{ active: currentFilter === '1w' }" @click="setFilter('1w')">۱ هفته</button>
      <button class="dash-btn" :class="{ active: currentFilter === '1d' }" @click="setFilter('1d')">امروز</button>
      <button class="dash-btn flex-center" :class="{ active: currentFilter === 'custom' }" @click="showCustomDateModal = true">
        <svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect>
          <line x1="16" y1="2" x2="16" y2="6"></line>
          <line x1="8" y1="2" x2="8" y2="6"></line>
          <line x1="3" y1="10" x2="21" y2="10"></line>
        </svg>
      </button>
    </div>

    <div class="financial-bento-grid">
      <div class="bento-card theme-sales">
        <div class="bento-info">
          <span class="bento-title">سود فروش</span>
          <span class="bento-subtitle">محقق‌شده خالص</span>
          <div class="bento-value-box">
             <span class="bento-value dir-ltr">{{ formatNumber(dashStats.salesRealized) }}</span>
             <span class="bento-curr">تومان</span>
          </div>
        </div>
        <div class="bento-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round">
            <path d="M6 2L3 6v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2V6l-3-4z"></path>
            <line x1="3" y1="6" x2="21" y2="6"></line>
            <path d="M16 10a4 4 0 0 1-8 0"></path>
          </svg>
        </div>
      </div>

      <div class="bento-card theme-repairs">
        <div class="bento-info">
          <span class="bento-title">سود تعمیرات</span>
          <span class="bento-subtitle">اجرت محقق‌شده</span>
          <div class="bento-value-box">
             <span class="bento-value dir-ltr">{{ formatNumber(dashStats.repairsRealized) }}</span>
             <span class="bento-curr">تومان</span>
          </div>
        </div>
        <div class="bento-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round">
            <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path>
          </svg>
        </div>
      </div>

      <div class="bento-card theme-unrealized">
        <div class="bento-info">
          <span class="bento-title">سود دفتری</span>
          <span class="bento-subtitle">در انتظار وصول</span>
          <div class="bento-value-box">
             <span class="bento-value dir-ltr">{{ formatNumber(dashStats.unrealized) }}</span>
             <span class="bento-curr">تومان</span>
          </div>
        </div>
        <div class="bento-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round">
            <line x1="12" y1="1" x2="12" y2="23"></line>
            <path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"></path>
          </svg>
        </div>
      </div>

      <div class="bento-card theme-customer-debt">
        <div class="bento-info">
          <span class="bento-title">مطالبات مشتریان</span>
          <span class="bento-subtitle">نسیه و اقساط</span>
          <div class="bento-value-box">
             <span class="bento-value dir-ltr">{{ formatNumber(dashStats.customerDebt) }}</span>
             <span class="bento-curr">تومان</span>
          </div>
        </div>
        <div class="bento-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round">
            <path d="M20 12V8H6a2 2 0 0 1-2-2c0-1.1.9-2 2-2h12v4"></path>
            <path d="M4 6v12c0 1.1.9 2 2 2h14v-4"></path>
            <path d="M18 12a2 2 0 0 0-2 2c0 1.1.9 2 2 2h4v-4h-4z"></path>
          </svg>
        </div>
      </div>

      <div class="bento-card theme-total-debt">
        <div class="bento-info">
          <span class="bento-title">بدهی ما (پرداختی)</span>
          <span class="bento-subtitle">به اشخاص و همکاران</span>
          <div class="bento-value-box">
             <span class="bento-value dir-ltr">{{ formatNumber(dashStats.partnerWeOwe) }}</span>
             <span class="bento-curr">تومان</span>
          </div>
        </div>
        <div class="bento-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round">
            <path d="M16 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path>
            <circle cx="8.5" cy="7" r="4"></circle>
            <line x1="18" y1="8" x2="23" y2="13"></line>
            <line x1="23" y1="8" x2="18" y2="13"></line>
          </svg>
        </div>
      </div>

      <div class="bento-card theme-total-creditors">
        <div class="bento-info">
          <span class="bento-title">طلب ما (دریافتی)</span>
          <span class="bento-subtitle">از اشخاص و همکاران</span>
          <div class="bento-value-box">
             <span class="bento-value dir-ltr">{{ formatNumber(dashStats.partnerTheyOwe) }}</span>
             <span class="bento-curr">تومان</span>
          </div>
        </div>
        <div class="bento-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round">
            <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path>
            <circle cx="9" cy="7" r="4"></circle>
            <path d="M19 11l2 2 4-4"></path>
          </svg>
        </div>
      </div>
    </div>

    <!-- هشدارها -->
    <div class="alerts-wrapper mb-lg">
      <div v-if="dashStats.loss > 0" class="alert-box alert-danger mb-sm" style="background: linear-gradient(135deg, #fff1f2, #ffe4e6); border-color: #fecdd3; border-right-color: #e11d48;">
        <div class="d-flex align-center gap-sm">
          <div class="alert-dot danger"></div>
          <strong class="text-danger-dark">هشدار زیان سیستم</strong>
        </div>
        <div class="d-flex align-center gap-xs mt-xs text-danger-dark" style="font-size: 13px;">
          <span>زیان خالص شما در این بازه:</span>
          <strong class="dir-ltr">{{ formatNumber(dashStats.loss) }}</strong>
          <span style="font-size: 10px;">تومان</span>
        </div>
      </div>

      <div v-if="activeDebtorsCount > 0" class="alert-box alert-warning">
        <div class="d-flex align-center gap-sm">
          <div class="alert-dot warning"></div>
          <strong class="text-warning-dark">اقدام جهت وصول مطالبات</strong>
        </div>
        <span class="text-sm text-warning-dark opacity-90">
          شما <strong class="font-900">{{ activeDebtorsCount }}</strong> پرونده با طلب تسویه نشده (نسیه/قسط) در این بازه دارید.
        </span>
      </div>
      <div v-else-if="dashStats.loss <= 0" class="alert-box alert-success">
        <div class="d-flex align-center gap-sm">
          <div class="alert-dot success"></div>
          <strong class="text-success-dark">وضعیت کاملاً پایدار</strong>
        </div>
        <span class="text-sm text-muted">هیچ مطالبه معوقه یا زیانی در این بازه وجود ندارد.</span>
      </div>
    </div>

    <!-- دکمه تنظیمات -->
    <div class="settings-action-card" @click="handleSettingsClick">
      <div class="lac-info">
        <span class="lac-title">تنظیمات سیستم</span>
        <span class="lac-subtitle">مدیریت داده‌ها، پشتیبان‌گیری و پاکسازی</span>
      </div>
      <div class="lac-icon settings-icon">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <circle cx="12" cy="12" r="3"></circle>
          <path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path>
        </svg>
      </div>
    </div>

    <!-- مودال تاریخ سفارشی -->
    <div v-if="showCustomDateModal" class="modal-overlay" @click.self="showCustomDateModal = false">
      <div class="modal-container modal-sm">
        <div class="modal-header">
          <h3 class="modal-title">بازه زمانی دلخواه</h3>
          <button class="btn-close" @click="showCustomDateModal = false">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
          </button>
        </div>
        <div class="modal-body">
          <div class="d-flex gap-md mb-sm flex-mobile-col">
            <div class="form-group flex-1">
              <label>از تاریخ:</label>
              <input v-model="customStartDate" type="text" class="form-control text-center dir-ltr" placeholder="مثال: 1402/01/01" />
            </div>
            <div class="form-group flex-1">
              <label>تا تاریخ:</label>
              <input v-model="customEndDate" type="text" class="form-control text-center dir-ltr" placeholder="مثال: 1402/12/29" />
            </div>
          </div>
        </div>
        <div class="modal-footer d-flex gap-sm flex-mobile-col">
          <button class="btn btn-primary flex-1 m-0" @click="applyCustomDate">اعمال فیلتر</button>
          <button class="btn btn-secondary flex-1 m-0" @click="showCustomDateModal = false">انصراف</button>
        </div>
      </div>
    </div>

    <input type="file" ref="restoreFileInput" accept=".json" style="display: none" @change="handleFileRestore" />

    <!-- مودال تنظیمات سیستم -->
    <div v-if="showSettingsModal" class="modal-overlay" @click.self="showSettingsModal = false">
      <div class="settings-modal-wrapper">
        <div class="settings-modal-header">
          <button v-if="activeSettingsTab !== 'main'" class="settings-back-btn" @click="activeSettingsTab = 'main'" title="بازگشت">
            <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 14 4 9 9 4"></polyline><path d="M20 20v-7a4 4 0 0 0-4-4H4"></path></svg>
          </button>

          <div class="settings-header-icon" :class="headerIconClass">
            <svg v-if="activeSettingsTab === 'main'" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="3"></circle><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path></svg>
            <svg v-else-if="activeSettingsTab === 'backup'" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path><polyline points="17 8 12 3 7 8"></polyline><line x1="12" y1="3" x2="12" y2="15"></line></svg>
            <svg v-else-if="activeSettingsTab === 'danger'" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></svg>
          </div>
          <div class="settings-header-text">
            <h3 class="settings-modal-title">
              {{ activeSettingsTab === 'main' ? 'تنظیمات سیستم' : (activeSettingsTab === 'backup' ? 'بکاپ و بازیابی اطلاعات' : 'منطقه خطر (حذف اطلاعات)') }}
            </h3>
            <span class="settings-modal-subtitle">
              {{ activeSettingsTab === 'main' ? 'انتخاب بخش مورد نظر' : 'مدیریت و تغییرات دیتابیس' }}
            </span>
          </div>
          <button class="settings-close-btn" @click="showSettingsModal = false">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
          </button>
        </div>
        
        <div class="settings-modal-body tab-content-animate" :key="activeSettingsTab">
          <template v-if="activeSettingsTab === 'main'">
            <div class="settings-menu-grid">
              <div class="menu-nav-card theme-nav-blue" @click="activeSettingsTab = 'backup'">
                <div class="menu-nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><ellipse cx="12" cy="5" rx="9" ry="3"></ellipse><path d="M21 12c0 1.66-4 3-9 3s-9-1.34-9-3"></path><path d="M3 5v14c0 1.66 4 3 9 3s9-1.34 9-3V5"></path></svg></div>
                <div class="menu-nav-content"><h4>مدیریت داده‌ها</h4><p>تهیه فایل پشتیبان و بازیابی اطلاعات سیستمی</p></div>
                <div class="menu-nav-arrow"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg></div>
              </div>

              <div class="menu-nav-card theme-nav-red" @click="activeSettingsTab = 'danger'">
                <div class="menu-nav-icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></svg></div>
                <div class="menu-nav-content"><h4>حذف اطلاعات</h4><p>پاکسازی کامل دیتابیس (غیرقابل بازگشت)</p></div>
                <div class="menu-nav-arrow"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg></div>
              </div>
            </div>
          </template>

          <template v-else-if="activeSettingsTab === 'backup'">
            <div class="settings-group-card">
              <div class="settings-card-left">
                <div class="settings-icon-box bg-blue-soft"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path><polyline points="7 10 12 15 17 10"></polyline><line x1="12" y1="15" x2="12" y2="3"></line></svg></div>
                <div class="settings-card-info"><h4>پشتیبان‌گیری از سیستم</h4><p>دریافت فایل خروجی امن (JSON) از تمامی اطلاعات ثبت شده</p></div>
              </div>
              <button class="settings-action-btn btn-blue" @click="backupData"><span>تهیه بکاپ</span><svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"></polyline></svg></button>
            </div>

            <div class="settings-group-card">
              <div class="settings-card-left">
                <div class="settings-icon-box bg-amber-soft"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path><polyline points="17 8 12 3 7 8"></polyline><line x1="12" y1="3" x2="12" y2="15"></line></svg></div>
                <div class="settings-card-info"><h4>بازیابی اطلاعات</h4><p>بارگذاری فایل پشتیبان و جایگزینی داده‌ها در سیستم</p></div>
              </div>
              <button class="settings-action-btn btn-amber" @click="restoreData"><span>بازیابی</span><svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"></polyline></svg></button>
            </div>
          </template>

          <template v-else-if="activeSettingsTab === 'danger'">
            <div class="alert-box alert-danger mb-sm">
              <div class="d-flex align-center gap-sm"><div class="alert-dot danger"></div><strong class="text-danger-dark">هشدار بسیار مهم!</strong></div>
              <span class="text-sm text-muted">با انجام این عملیات، تمامی اطلاعات شامل فاکتورها، اقساط، تعمیرات و همکاران برای همیشه پاک خواهند شد.</span>
            </div>
            <div class="settings-group-card danger-card-zone mt-sm">
              <div class="settings-card-left">
                <div class="settings-icon-box bg-red-soft"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></svg></div>
                <div class="settings-card-info"><h4 class="text-danger-title">حذف کل اطلاعات سیستم</h4><p>پاکسازی کامل دیتابیس دستگاه</p></div>
              </div>
              <button class="settings-action-btn btn-red-solid" @click="clearAllData"><span>پاکسازی کامل</span></button>
            </div>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onActivated } from 'vue';

const props = defineProps({
  sales: { type: Array, default: () => [] },
  repairs: { type: Array, default: () => [] },
  partners: { type: Array, default: () => [] }
});

const emit = defineEmits(['change-view']);
const { formatNumber, getFinancials, getRepairFinancials } = window.SysUtils;

const currentFilter = ref('all');
const showCustomDateModal = ref(false);
const customStartDate = ref('');
const customEndDate = ref('');

const showSettingsModal = ref(false);
const activeSettingsTab = ref('main');
const restoreFileInput = ref(null);

const localSales = ref([]);
const localRepairs = ref([]);
const localPartners = ref([]);

let lastLoadTime = 0;
const showBackupWarning = ref(true);

const loadFromDB = async () => {
  if (window.DB) {
    const now = Date.now();
    if (now - lastLoadTime < 3000 && localSales.value.length > 0) return;
    
    try {
      localSales.value = await window.DB.getAll('sales');
      localRepairs.value = await window.DB.getAll('repairs');
      localPartners.value = await window.DB.getAll('partners');
      lastLoadTime = now;
    } catch (e) {
      console.error('خطا در فراخوانی دیتابیس:', e);
    }
  }
};

onMounted(() => {
  if (props.sales.length === 0 && props.repairs.length === 0 && props.partners.length === 0) {
    loadFromDB();
  }
});
onActivated(() => { loadFromDB(); });

const activeSales = computed(() => props.sales.length ? props.sales : localSales.value);
const activeRepairs = computed(() => props.repairs.length ? props.repairs : localRepairs.value);
const activePartners = computed(() => props.partners.length ? props.partners : localPartners.value);

const getItemTimestamp = (item) => {
  if (item.timestamp && !isNaN(item.timestamp)) return Number(item.timestamp);
  if (item.id && !isNaN(item.id)) return Number(item.id);
  return 0;
};

const filterDataByDate = (data) => {
  if (!data || !Array.isArray(data)) return [];
  if (currentFilter.value === 'all') return data;
  
  const now = Date.now();
  const oneDay = 24 * 60 * 60 * 1000;
  
  if (currentFilter.value !== 'custom') {
    let limitMs = 0;
    if (currentFilter.value === '1d') limitMs = now - oneDay;
    else if (currentFilter.value === '1w') limitMs = now - (7 * oneDay);
    else if (currentFilter.value === '1m') limitMs = now - (30 * oneDay);
    else if (currentFilter.value === '3m') limitMs = now - (90 * oneDay);
    
    return data.filter(item => {
      const ts = getItemTimestamp(item);
      if (!ts) return true;
      return ts >= limitMs;
    });
  }
  
  if (currentFilter.value === 'custom') {
    return data.filter(item => {
      const itemDate = item.date || '';
      let match = true;
      if (customStartDate.value && itemDate < customStartDate.value) match = false;
      if (customEndDate.value && itemDate > customEndDate.value) match = false;
      return match;
    });
  }
  
  return data;
};

const filteredSales = computed(() => filterDataByDate(activeSales.value));
const filteredRepairs = computed(() => filterDataByDate(activeRepairs.value));
const filteredPartners = computed(() => filterDataByDate(activePartners.value));

const dashStats = computed(() => {
  let salesRealized = 0, repairsRealized = 0;
  let unrealized = 0, loss = 0;
  let customerDebt = 0;

  filteredSales.value.forEach(s => {
    const fin = getFinancials(s);
    salesRealized += fin.realizedProfit;
    if (fin.realizedProfit < 0) loss += Math.abs(fin.realizedProfit);
    if (fin.debt > 0 && fin.expectedProfit > 0) {
      unrealized += Math.max(0, fin.expectedProfit - Math.max(0, fin.realizedProfit));
    }
    customerDebt += fin.debt;
  });

  filteredRepairs.value.forEach(r => {
    const fin = getRepairFinancials(r);
    repairsRealized += fin.realizedProfit;
    if (fin.realizedProfit < 0) loss += Math.abs(fin.realizedProfit);
    if (fin.debt > 0 && fin.expectedProfit > 0) {
      unrealized += Math.max(0, fin.expectedProfit - Math.max(0, fin.realizedProfit));
    }
    customerDebt += fin.debt;
  });

  let partnerWeOwe = 0, partnerTheyOwe = 0;
  filteredPartners.value.forEach(p => {
    const bal = Number(p.balance || 0);
    if (bal > 0) partnerWeOwe += bal;
    else if (bal < 0) partnerTheyOwe += Math.abs(bal);
  });

  return {
    salesRealized, repairsRealized, totalRealized: salesRealized + repairsRealized,
    unrealized, loss, customerDebt, partnerWeOwe, partnerTheyOwe
  };
});

const activeDebtorsCount = computed(() => {
  let count = 0;
  filteredSales.value.forEach(s => { if (getFinancials(s).debt > 0) count++; });
  filteredRepairs.value.forEach(r => { if (getRepairFinancials(r).debt > 0) count++; });
  return count;
});

const setFilter = (type) => {
  currentFilter.value = type;
};

const applyCustomDate = () => {
  currentFilter.value = 'custom';
  showCustomDateModal.value = false;
};

const handleSettingsClick = () => {
  activeSettingsTab.value = 'main';
  showSettingsModal.value = true;
};

const headerIconClass = computed(() => {
  if (activeSettingsTab.value === 'main') return 'bg-icon-primary';
  if (activeSettingsTab.value === 'backup') return 'bg-icon-blue';
  if (activeSettingsTab.value === 'danger') return 'bg-icon-red';
  return 'bg-icon-primary';
});

// تابع بکاپ‌گیری با قابلیت اشتراک‌گذاری مستقیم در آیفون
const backupData = async () => {
  try {
    const backupPayload = {
      version: '1.0',
      exportDate: new Date().toISOString(),
      sales: activeSales.value,
      repairs: activeRepairs.value,
      partners: activePartners.value
    };

    const jsonString = JSON.stringify(backupPayload, null, 2);
    const blob = new Blob([jsonString], { type: 'application/json' });
    const fileName = `backup_accounting_${new Date().toLocaleDateString('fa-IR').replace(/\//g, '-')}.json`;

    if (navigator.share && navigator.canShare) {
      const file = new File([blob], fileName, { type: 'application/json' });
      if (navigator.canShare({ files: [file] })) {
        await navigator.share({
          files: [file],
          title: 'بکاپ سیستم حسابداری',
          text: 'فایل پشتیبان اطلاعات حسابداری شما'
        });
        return;
      }
    }

    const url = URL.createObjectURL(blob);
    const downloadAnchor = document.createElement('a');
    downloadAnchor.setAttribute("href", url);
    downloadAnchor.setAttribute("download", fileName);
    document.body.appendChild(downloadAnchor);
    downloadAnchor.click();
    downloadAnchor.remove();
    URL.revokeObjectURL(url);

  } catch (e) {
    alert('خطا در تهیه فایل پشتیبان: ' + e.message);
  }
};

const restoreData = () => {
  if (restoreFileInput.value) {
    restoreFileInput.value.click();
  }
};

const handleFileRestore = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  const reader = new FileReader();
  reader.onload = async (e) => {
    try {
      const data = JSON.parse(e.target.result);
      if (window.DB) {
        await window.DB.restoreBackup(data);
        alert('اطلاعات با موفقیت بازیابی شدند.');
        await loadFromDB();
        showSettingsModal.value = false;
      }
    } catch (err) {
      alert('خطا در بازیابی اطلاعات: ' + err.message);
    }
  };
  reader.readAsText(file);
  event.target.value = '';
};

const clearAllData = async () => {
  const stepOne = confirm('هشدار مهم!\nآیا از حذف تمامی اطلاعات سیستم اطمینان دارید؟');
  if (stepOne) {
    const stepTwo = confirm('آیا واقعاً مطمئن هستید؟ تمامی داده‌ها برای همیشه پاک خواهند شد!');
    if (stepTwo) {
      if (window.DB) {
        try {
          await window.DB.clearAll();
          alert('تمامی اطلاعات سیستم با موفقیت حذف گردید.');
          localSales.value = [];
          localRepairs.value = [];
          localPartners.value = [];
          showSettingsModal.value = false;
        } catch (e) {
          console.error(e);
        }
      }
    }
  }
};
</script>

<style scoped>
.dashboard-page {
  --primary: #3b82f6; 
  --primary-dark: #2563eb; 
  --primary-light: #eff6ff;
  --success: #10b981; 
  --success-dark: #059669; 
  --success-light: #ecfdf5;
  --warning: #f59e0b; 
  --warning-dark: #d97706; 
  --warning-light: #fffbeb;
  --danger: #ef4444;  
  --danger-dark: #e11d48;  
  --danger-light: #fff1f2;
  --purple: #8b5cf6;  
  --purple-light: #f5f3ff;
  
  --bg-color: #f8fafc; 
  --surface: #ffffff;
  --text-dark: #0f172a; 
  --text-muted: #475569; 
  --text-light: #94a3b8;
  --border: #e2e8f0; 
  --border-hover: #cbd5e1;

  --radius-sm: 8px; 
  --radius-md: 12px; 
  --radius-lg: 16px; 
  --radius-xl: 20px;
  --transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);

  font-family: 'Vazirmatn', Tahoma, sans-serif !important;
  direction: rtl;
  text-align: right;
  padding-bottom: 20px;
  overflow-x: hidden;
  width: 100%;
}

@keyframes gradientBG {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}
@keyframes floatOrb {
  0% { transform: translateY(0) scale(1); }
  50% { transform: translateY(-15px) scale(1.05); }
  100% { transform: translateY(0) scale(1); }
}

.main-profit-card { 
  position: relative; 
  background: linear-gradient(135deg, #1e3a8a, #3b82f6, #4f46e5, #3b82f6); 
  background-size: 300% 300%;
  animation: gradientBG 10s ease infinite;
  border-radius: 28px; 
  padding: 36px 24px 0 24px; 
  color: #ffffff; 
  overflow: hidden; 
  box-shadow: 0 20px 40px -10px rgba(37, 99, 235, 0.5), inset 0 1px 1px rgba(255, 255, 255, 0.3); 
  margin-bottom: 20px; 
  height: 250px; 
  display: flex; 
  flex-direction: column; 
  align-items: center; 
  border: 1px solid rgba(255,255,255,0.1);
}

.profit-orb-1, .profit-orb-2 {
  position: absolute;
  border-radius: 50%;
  filter: blur(40px);
  z-index: 0;
  opacity: 0.6;
  pointer-events: none;
}
.profit-orb-1 { width: 200px; height: 200px; background: #93c5fd; top: -50px; left: -50px; animation: floatOrb 6s ease-in-out infinite; }
.profit-orb-2 { width: 250px; height: 250px; background: #818cf8; bottom: -50px; right: -50px; animation: floatOrb 8s ease-in-out infinite reverse; }

.profit-content-wrapper { display: flex; flex-direction: column; align-items: center; z-index: 2; position: relative; width: 100%; margin-top: 10px; }
.profit-title { font-size: 14px; font-weight: 800; color: rgba(255, 255, 255, 0.9); margin-bottom: 12px; display: flex; align-items: center; gap: 8px; background: rgba(0, 0, 0, 0.15); padding: 8px 16px; border-radius: 20px; backdrop-filter: blur(10px); border: 1px solid rgba(255,255,255,0.1); }
.profit-amount-container { display: flex; align-items: center; justify-content: center; gap: 4px; direction: rtl; }
.profit-currency { font-size: 16px; font-weight: 600; color: #dbeafe; padding-top: 8px; opacity: 0.8;}
.profit-amount { font-size: 46px; font-weight: 900; color: #ffffff; letter-spacing: -1px; line-height: 1; text-shadow: 0 10px 20px rgba(0, 0, 0, 0.3); }
.profit-svg-bg { position: absolute; bottom: 0; left: 0; width: 100%; height: 50%; z-index: 1; pointer-events: none; opacity: 0.9; }

.dash-filters-container { 
  background: var(--surface); 
  border-radius: 20px; 
  padding: 6px; 
  display: flex; 
  justify-content: space-between; 
  align-items: center; 
  gap: 4px; 
  flex-wrap: wrap; 
  box-shadow: 0 4px 15px -3px rgba(15, 23, 42, 0.04); 
  border: 1px solid rgba(226, 232, 240, 0.8); 
  margin-bottom: 24px; 
  width: 100%;
}
.dash-filters-container .dash-btn { flex: 1 1 auto; background: transparent; color: var(--text-muted); border: none; border-radius: 14px; padding: 10px 0; font-size: 12px; font-weight: 700; cursor: pointer; transition: var(--transition); }
.dash-filters-container .dash-btn.active { background: var(--primary); color: #ffffff; box-shadow: 0 4px 10px rgba(37, 99, 235, 0.3); }

/* گرید محدود شده برای جلوگیری از بیرون‌زدگی در گوشی */
.financial-bento-grid { 
  display: grid; 
  grid-template-columns: repeat(2, minmax(0, 1fr)); 
  gap: 10px; 
  margin-bottom: 16px; 
  width: 100%;
}
.bento-card { 
  background: var(--surface); 
  border-radius: 16px; 
  padding: 12px; 
  display: flex; 
  justify-content: space-between; 
  align-items: center; 
  box-shadow: 0 4px 15px -3px rgba(15, 23, 42, 0.04); 
  border: 1px solid rgba(226, 232, 240, 0.8); 
  min-height: 80px; 
  gap: 6px; 
}
.bento-info { display: flex; flex-direction: column; align-items: flex-start; justify-content: center; gap: 2px; flex: 1; min-width: 0; }
.bento-title { font-size: 11.5px; font-weight: 800; color: var(--text-muted); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 100%; }
.bento-subtitle { font-size: 9px; font-weight: 600; color: var(--text-light); margin-bottom: 3px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 100%; }
.bento-value-box { display: flex; align-items: baseline; gap: 4px; direction: rtl; flex-wrap: wrap; }
.bento-value { font-size: 14px; font-weight: 900; color: var(--text-dark); line-height: 1.2; word-break: break-word; }
.bento-curr { font-size: 9px; font-weight: 800; color: var(--text-muted); opacity: 0.8; }
.bento-icon { width: 34px; height: 34px; border-radius: 10px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.bento-icon svg { width: 16px; height: 16px; stroke-width: 2.2; }

.theme-sales { background: linear-gradient(145deg, #ffffff, #f0fdf4); }
.theme-sales .bento-icon { background: var(--success-light); color: var(--success-dark); box-shadow: 0 6px 14px -2px rgba(16, 185, 129, 0.35); }
.theme-repairs { background: linear-gradient(145deg, #ffffff, #eff6ff); }
.theme-repairs .bento-icon { background: var(--primary-light); color: var(--primary-dark); box-shadow: 0 6px 14px -2px rgba(59, 130, 246, 0.35); }
.theme-unrealized { background: linear-gradient(145deg, #ffffff, #f5f3ff); border-color: #ddd6fe; }
.theme-unrealized .bento-icon { background: var(--purple-light); color: #7c3aed; box-shadow: 0 6px 14px -2px rgba(139, 92, 246, 0.35); }
.theme-customer-debt { background: linear-gradient(145deg, #ffffff, #fffbeb); border-color: #fde68a; }
.theme-customer-debt .bento-icon { background: var(--warning-light); color: var(--warning-dark); box-shadow: 0 6px 14px -2px rgba(245, 158, 11, 0.35); }
.theme-total-debt { background: linear-gradient(145deg, #ffffff, #fff5f5); border-color: #fecdd3; }
.theme-total-debt .bento-icon { background: #ffe4e6; color: #e11d48; box-shadow: 0 6px 14px -2px rgba(225, 29, 72, 0.3); }
.theme-total-creditors { background: linear-gradient(145deg, #ffffff, #f0fdf4); border-color: #bbf7d0; }
.theme-total-creditors .bento-icon { background: #dcfce7; color: #15803d; box-shadow: 0 6px 14px -2px rgba(21, 128, 61, 0.3); }

.settings-action-card {
  background: var(--surface);
  border-radius: 20px;
  padding: 18px 22px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 8px 20px -6px rgba(15, 23, 42, 0.08);
  border: 1px solid rgba(226, 232, 240, 0.8);
  margin-bottom: 20px;
  cursor: pointer;
  transition: var(--transition);
  position: relative;
  overflow: hidden;
  width: 100%;
  box-sizing: border-box;
}
.settings-action-card::before { content: ''; position: absolute; top: 0; right: 0; width: 4px; height: 100%; background: var(--text-muted); border-radius: 0 8px 8px 0; transition: var(--transition); }
.settings-action-card:hover { border-color: var(--border-hover); transform: translateY(-2px); box-shadow: 0 12px 25px -8px rgba(15, 23, 42, 0.12); }
.settings-action-card:hover::before { background: var(--primary); }
.settings-action-card:active { transform: scale(0.98); }
.lac-info { display: flex; flex-direction: column; gap: 4px; }
.lac-title { font-size: 15px; font-weight: 900; color: var(--text-dark); }
.lac-subtitle { font-size: 11px; font-weight: 600; color: var(--text-muted); }
.settings-icon { width: 44px; height: 44px; background: var(--bg-color); border-radius: 14px; display: flex; align-items: center; justify-content: center; color: var(--text-dark); border: 1px solid var(--border); box-shadow: 0 2px 5px rgba(0,0,0,0.02); flex-shrink: 0; }
.settings-icon svg { width: 22px; height: 22px; transition: transform 0.5s cubic-bezier(0.34, 1.56, 0.64, 1); }
.settings-action-card:hover .settings-icon svg { transform: rotate(90deg); color: var(--primary); }

.alert-box { border-radius: 12px; padding: 14px 16px; margin-bottom: 12px; display: flex; flex-direction: column; gap: 8px; background: var(--surface); box-shadow: 0 4px 10px -2px rgba(0,0,0,0.05); border: 1px solid var(--border); border-right-width: 4px; }
.alert-danger { background: linear-gradient(to right, #fff1f2, #ffffff); border-color: #fecdd3; border-right-color: var(--danger); }
.alert-warning { background: linear-gradient(to right, #fffbeb, #ffffff); border-color: #fde68a; border-right-color: var(--warning); }
.alert-success { background: linear-gradient(to right, #ecfdf5, #ffffff); border-color: #a7f3d0; border-right-color: var(--success); }
.alert-dot { width: 10px; height: 10px; border-radius: 50%; }
.alert-dot.danger { background: var(--danger); box-shadow: 0 0 8px var(--danger); }
.alert-dot.warning { background: var(--warning); box-shadow: 0 0 8px var(--warning); }
.alert-dot.success { background: var(--success); box-shadow: 0 0 8px var(--success); }

.settings-modal-wrapper { background: var(--surface); width: 100%; max-width: 440px; border-radius: 24px; overflow: hidden; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25); border: 1px solid var(--border); }
.settings-modal-header { padding: 20px; background: var(--bg-color); border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 12px; position: relative; }
.settings-back-btn, .settings-close-btn { background: transparent; border: none; cursor: pointer; color: var(--text-muted); display: flex; align-items: center; justify-content: center; padding: 4px; border-radius: 8px; transition: var(--transition); }
.settings-back-btn:hover, .settings-close-btn:hover { background: var(--border); color: var(--text-dark); }
.settings-close-btn { margin-right: auto; }
.settings-header-icon { width: 42px; height: 42px; border-radius: 12px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.settings-header-icon svg { width: 22px; height: 22px; }
.bg-icon-primary { background: var(--primary-light); color: var(--primary-dark); }
.bg-icon-blue { background: #dbeafe; color: #1e40af; }
.bg-icon-red { background: var(--danger-light); color: var(--danger-dark); }

.settings-header-text { display: flex; flex-direction: column; }
.settings-modal-title { font-size: 15px; font-weight: 800; color: var(--text-dark); }
.settings-modal-subtitle { font-size: 11px; color: var(--text-muted); }
.settings-modal-body { padding: 20px; }

.settings-menu-grid { display: flex; flex-direction: column; gap: 12px; }
.menu-nav-card { display: flex; align-items: center; gap: 14px; padding: 16px; border-radius: 16px; border: 1px solid var(--border); cursor: pointer; transition: var(--transition); }
.menu-nav-card:hover { transform: translateY(-2px); }
.theme-nav-blue { background: linear-gradient(145deg, #ffffff, #eff6ff); }
.theme-nav-blue:hover { border-color: #93c5fd; }
.theme-nav-blue .menu-nav-icon { background: var(--primary-light); color: var(--primary-dark); }
.theme-nav-red { background: linear-gradient(145deg, #ffffff, #fff1f2); }
.theme-nav-red:hover { border-color: #fca5a5; }
.theme-nav-red .menu-nav-icon { background: var(--danger-light); color: var(--danger-dark); }

.menu-nav-icon { width: 40px; height: 40px; border-radius: 10px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.menu-nav-icon svg { width: 20px; height: 20px; }
.menu-nav-content { flex: 1; }
.menu-nav-content h4 { font-size: 13px; font-weight: 800; color: var(--text-dark); margin-bottom: 2px; }
.menu-nav-content p { font-size: 11px; color: var(--text-muted); }
.menu-nav-arrow { color: var(--text-light); }

.settings-group-card { display: flex; justify-content: space-between; align-items: center; padding: 14px; border: 1px solid var(--border); border-radius: 14px; margin-bottom: 12px; background: var(--surface); gap: 10px; }
.settings-group-card:last-child { margin-bottom: 0; }
.settings-card-left { display: flex; align-items: center; gap: 12px; flex: 1; min-width: 0; }
.settings-icon-box { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.settings-icon-box svg { width: 18px; height: 18px; }
.bg-blue-soft { background: #eff6ff; color: #2563eb; }
.bg-amber-soft { background: #fffbeb; color: #d97706; }
.bg-red-soft { background: #fff1f2; color: #e11d48; }

.settings-card-info h4 { font-size: 12px; font-weight: 800; color: var(--text-dark); margin-bottom: 2px; }
.settings-card-info p { font-size: 10px; color: var(--text-muted); line-height: 1.4; }
.text-danger-title { color: var(--danger-dark) !important; }

.settings-action-btn { padding: 8px 14px; border-radius: 10px; border: none; font-size: 11px; font-weight: 800; cursor: pointer; display: flex; align-items: center; gap: 6px; white-space: nowrap; transition: var(--transition); }
.btn-blue { background: #2563eb; color: #ffffff; }
.btn-blue:hover { background: #1d4ed8; }
.btn-amber { background: #d97706; color: #ffffff; }
.btn-amber:hover { background: #b45309; }
.btn-red-solid { background: var(--danger-dark); color: #ffffff; }
.btn-red-solid:hover { background: #be123c; }

.danger-card-zone { border-color: #fecdd3; background: #fff5f5; }
.tab-content-animate { animation: fadeIn 0.25s ease-in-out; }
.dir-ltr { direction: ltr; display: inline-block; }
.modal-overlay { position: fixed; inset: 0; background: rgba(15, 23, 42, 0.65); backdrop-filter: blur(6px); -webkit-backdrop-filter: blur(6px); z-index: 1100; display: flex; justify-content: center; align-items: center; direction: rtl; padding: 16px; transition: var(--transition); }
.modal-container { background: var(--surface); width: 100%; max-height: 90vh; border-radius: var(--radius-xl); box-shadow: var(--shadow-float); display: flex; flex-direction: column; overflow: hidden; animation: modalScaleIn 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); border: 1px solid rgba(255, 255, 255, 0.2); }
.modal-header { display: flex; justify-content: space-between; align-items: center; padding: 16px 20px; border-bottom: 1px solid var(--border); background: var(--bg-color); }
.modal-title { font-size: 15px; font-weight: 900; color: var(--text-dark); margin: 0; }
.modal-body { padding: 20px; overflow-y: auto; flex: 1; -webkit-overflow-scrolling: touch; }
.modal-footer { padding: 16px 20px; border-top: 1px solid var(--border); background: var(--bg-color); }
.modal-sm { max-width: 320px; }
.btn-close { background: var(--bg-color); border: none; width: 32px; height: 32px; border-radius: 50%; color: var(--text-muted); cursor: pointer; display: flex; align-items: center; justify-content: center; transition: var(--transition); }
.btn-close:active { background: var(--border); transform: scale(0.9); }
.form-group { margin-bottom: 16px; }
.form-group label { display: block; margin-bottom: 8px; font-weight: 700; font-size: 11px; color: var(--text-muted); }
.form-control { width: 100%; min-width: 0; min-height: 44px; padding: 10px 14px; border: 1.5px solid var(--border); border-radius: var(--radius-md); background-color: var(--bg-color); font-size: 13px; outline: none; direction: rtl; font-weight: 700; text-align: right; color: var(--text-dark); transition: var(--transition); }
.form-control:focus { border-color: var(--primary); background-color: var(--surface); box-shadow: 0 0 0 4px var(--primary-light); }
.d-flex { display: flex; }
.gap-md { gap: 12px; }
.mb-sm { margin-bottom: 8px; }
.flex-1 { flex: 1; }
.m-0 { margin: 0 !important; }
.text-center { text-align: center !important; }
@media (max-width: 768px) { .flex-mobile-col { display: flex !important; flex-direction: column !important; gap: 12px !important; } }
.btn { width: 100%; min-height: 48px; padding: 12px 16px; border: none; border-radius: var(--radius-md); font-weight: 800; cursor: pointer; text-align: center; margin-top: 10px; transition: var(--transition); font-size: 14px; display: flex; justify-content: center; align-items: center; gap: 8px; }
.btn-primary { background-color: var(--primary-dark); color: white; box-shadow: 0 4px 12px rgba(37, 99, 235, 0.2); }
.btn-secondary { background-color: var(--border); color: var(--text-dark); }
@keyframes modalScaleIn { 0% { opacity: 0; transform: scale(0.95) translateY(10px); } 100% { opacity: 1; transform: scale(1) translateY(0); } }
</style>

