<template>
    <div id="app-container">
        <main id="main-content">
            
            <!-- پیام خطا بالاتر از کامپوننت قرار می‌گیرد تا چرخه کش را متوقف نکند -->
            <div v-if="systemError" style="background:#fee2e2; border:1px solid #fca5a5; padding:14px; border-radius:14px; color:#b91c1c; margin-bottom:16px; direction: rtl;">
                <strong style="font-size:14px;">❌ خطای بارگذاری صفحه:</strong>
                <p style="font-family:monospace; margin-top:8px; direction:ltr; text-align:left; font-size:12px; white-space: pre-wrap;">{{ systemError }}</p>
                <button @click="systemError = ''" style="margin-top:10px; padding:6px 12px; border-radius:8px; background:#b91c1c; color:white; border:none; cursor: pointer;">بستن خطا</button>
            </div>
            
            <!-- ساختار KeepAlive کاملا ایمن شده -->
            <KeepAlive>
                <component :is="currentComponent" @change-view="changeTab"></component>
            </KeepAlive>
            
        </main>

        <!-- منوی کپسولی پایین با سیستم مخفی‌شونده برای کیبورد و مودال‌ها -->
        <nav class="bottom-nav-wrapper" :class="{ 'nav-hidden': isKeyboardOpen || isAnyModalOpen }">
            <button class="nav-item" :class="{ active: activeTab === 'sales' }" @click="changeTab('sales')">
                <div class="nav-item-inner">
                    <svg viewBox="0 0 24 24"><path d="M6 2L3 6v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2V6l-3-4z"></path><line x1="3" y1="6" x2="21" y2="6"></line><path d="M16 10a4 4 0 0 1-8 0"></path></svg>
                    <span>فروش</span>
                </div>
            </button>
            <button class="nav-item" :class="{ active: activeTab === 'repairs' }" @click="changeTab('repairs')">
                <div class="nav-item-inner">
                    <svg viewBox="0 0 24 24"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path></svg>
                    <span>تعمیرات</span>
                </div>
            </button>
            <button class="nav-item" :class="{ active: activeTab === 'dashboard' }" @click="changeTab('dashboard')">
                <div class="nav-item-inner">
                    <svg viewBox="0 0 24 24"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect><line x1="3" y1="9" x2="21" y2="9"></line><line x1="9" y1="21" x2="9" y2="9"></line></svg>
                    <span>داشبورد</span>
                </div>
            </button>
            <button class="nav-item" :class="{ active: activeTab === 'installments' }" @click="changeTab('installments')">
                <div class="nav-item-inner">
                    <svg viewBox="0 0 24 24"><path d="M20 12V8H6a2 2 0 0 1-2-2c0-1.1.9-2 2-2h12v4"></path><path d="M4 6v12c0 1.1.9 2 2 2h14v-4"></path><path d="M18 12a2 2 0 0 0-2 2c0 1.1.9 2 2 2h4v-4h-4z"></path></svg>
                    <span>اقساط</span>
                </div>
            </button>
            <button class="nav-item" :class="{ active: activeTab === 'partners' }" @click="changeTab('partners')">
                <div class="nav-item-inner">
                    <svg viewBox="0 0 24 24"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path><circle cx="9" cy="7" r="4"></circle><path d="M23 21v-2a4 4 0 0 0-3-3.87"></path><path d="M16 3.13a4 4 0 0 1 0 7.75"></path></svg>
                    <span>حساب‌ها</span>
                </div>
            </button>
        </nav>
    </div>
</template>

<script>
const { ref, onMounted, shallowRef, markRaw } = Vue;
const { loadModule } = window['vue3-sfc-loader'];

export default {
    setup() {
        const activeTab = ref('dashboard');
        const currentComponent = shallowRef(null);
        const isKeyboardOpen = ref(false);
        const isAnyModalOpen = ref(false); 
        const systemError = ref('');
        
        const options = {
            moduleCache: { vue: Vue },
            async getFile(url) {
                const res = await fetch(url);
                if (!res.ok) throw new Error(`فایل یافت نشد یا مسیر اشتباه است: ${url} \n(کد خطا: ${res.status})`);
                return { getContentData: asBinary => asBinary ? res.arrayBuffer() : res.text() }
            },
            addStyle(textContent) {
                const style = Object.assign(document.createElement('style'), { textContent });
                document.head.appendChild(style);
            }
        };

        const loadView = async (viewName) => {
            try {
                systemError.value = '';
                const component = await loadModule(`./views/${viewName}.vue`, options);
                currentComponent.value = markRaw(component);
            } catch(e) {
                systemError.value = e.message || e;
                console.error("خطا در بارگذاری کامپوننت:", e);
            }
        };

        const changeTab = (tab) => {
            activeTab.value = tab;
            const viewMap = {
                'dashboard': 'Dashboard',
                'sales': 'Sales',
                'repairs': 'Repairs',
                'installments': 'Installments',
                'partners': 'Partners'
            };
            if (viewMap[tab]) {
                loadView(viewMap[tab]);
            }
        };

        onMounted(async () => {
            try {
                if (!window.DB) throw new Error("دیتابیس سیستم (DB) در دسترس نیست. فایل db.js لود نشده است.");
                await window.DB.init();
                changeTab('dashboard'); 

                const modalObserver = new MutationObserver(() => {
                    const hasModal = document.querySelector('.modal-overlay') !== null || document.querySelector('.form-container-anim') !== null;
                    if (isAnyModalOpen.value !== hasModal) {
                        isAnyModalOpen.value = hasModal;
                    }
                });
                
                modalObserver.observe(document.body, { childList: true, subtree: true });

                if (window.visualViewport) {
                    const initialHeight = window.visualViewport.height;
                    window.visualViewport.addEventListener('resize', () => {
                        if (window.visualViewport.height < initialHeight - 150) {
                            isKeyboardOpen.value = true;
                        } else {
                            isKeyboardOpen.value = false;
                        }
                    });
                } else {
                    const inputElements = ['input', 'textarea'];
                    document.addEventListener('focusin', (e) => {
                        const targetTag = e.target?.tagName?.toLowerCase() || '';
                        if (inputElements.includes(targetTag) && e.target.type !== 'radio' && e.target.type !== 'checkbox') {
                            isKeyboardOpen.value = true;
                        }
                    });
                    document.addEventListener('focusout', () => {
                        setTimeout(() => { isKeyboardOpen.value = false; }, 150); 
                    });
                }
            } catch(err) {
                systemError.value = "خطای اولیه در راه‌اندازی برنامه:\n" + (err.message || err);
            }
        });

        return { activeTab, currentComponent, changeTab, isKeyboardOpen, isAnyModalOpen, systemError };
    }
}
</script>

