<script>
import { useDisplay } from 'vuetify'
import VerticalNav from '@layouts/components/VerticalNav.vue'

export default defineComponent({
  setup(props, { slots }) {
    const isOverlayNavActive = ref(false)
    const isLayoutOverlayVisible = ref(false)
    const toggleIsOverlayNavActive = useToggle(isOverlayNavActive)
    const route = useRoute()
    const { mdAndDown } = useDisplay()


    // ℹ️ This is alternative to below two commented watcher
    // We want to show overlay if overlay nav is visible and want to hide overlay if overlay is hidden and vice versa.
    syncRef(isOverlayNavActive, isLayoutOverlayVisible)

    return () => {
      // 👉 Vertical nav
      const verticalNav = h(VerticalNav, { isOverlayNavActive: isOverlayNavActive.value, toggleIsOverlayNavActive }, {
        'nav-header': () => slots['vertical-nav-header']?.({ toggleIsOverlayNavActive }),
        'before-nav-items': () => slots['before-vertical-nav-items']?.(),
        'default': () => slots['vertical-nav-content']?.(),
        'after-nav-items': () => slots['after-vertical-nav-items']?.(),
      })


      // 👉 Navbar
      const navbar = h('header', { class: ['layout-navbar navbar-blur'] }, [
        h('div', { class: 'navbar-content-container' }, slots.navbar?.({
          toggleVerticalOverlayNavActive: toggleIsOverlayNavActive,
        })),
      ])

      const main = h('main', { class: 'layout-page-content' }, h('div', { class: 'page-content-container' }, slots.default?.()))


      // 👉 Footer
      const footer = h('footer', { class: 'layout-footer' }, [
        h('div', { class: 'footer-content-container' }, slots.footer?.()),
      ])


      // 👉 Overlay
      const layoutOverlay = h('div', {
        class: ['layout-overlay', { visible: isLayoutOverlayVisible.value }],
        onClick: () => { isLayoutOverlayVisible.value = !isLayoutOverlayVisible.value },
      })

      return h('div', {
        class: [
          'layout-wrapper layout-nav-type-vertical layout-navbar-static layout-footer-static layout-content-width-fluid',
          mdAndDown.value && 'layout-overlay-nav',
          route.meta.layoutWrapperClasses,
        ],
      }, [
        verticalNav,
        h('div', { class: 'layout-content-wrapper' }, [
          main,
        ]),
        layoutOverlay,
      ])
    }
  },
})
</script>

