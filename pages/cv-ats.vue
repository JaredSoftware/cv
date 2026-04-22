<template>
  <div>
    <!-- Botón para descargar PDF -->
    <div id="pdf-button" style="position: fixed; top: 20px; right: 20px; z-index: 1000;">
      <button
        @click="downloadPDF"
        style="padding: 10px 20px; background: #2563eb; color: white; border: none; border-radius: 5px; cursor: pointer; font-size: 14px; font-weight: bold; box-shadow: 0 2px 4px rgba(0,0,0,0.2);"
      >
        &#128229; {{ $t('cvAts.downloadPDF') }}
      </button>
    </div>

    <div id="cv-ats" class="cv-wrapper">
      <!-- SIDEBAR -->
      <aside class="cv-sidebar">

        <!-- Foto -->
        <div class="cv-photo-wrap">
          <img :src="profileImagePath" alt="Foto" class="cv-photo" />
        </div>

        <!-- Nombre en sidebar (mobile / visible en PDF pequeño) -->
        <div class="cv-sidebar-name">{{ $t('name') }}</div>

        <!-- Contacto -->
        <div class="cv-sidebar-section">
          <h3 class="cv-sidebar-title">{{ $t('sections.contactInfo') }}</h3>
          <ul class="cv-contact-list">
            <li><span class="cv-icon">&#9993;</span> jaredwesley27@hotmail.com</li>
            <li><span class="cv-icon">&#9990;</span> +57 322 926 6046</li>
            <li><span class="cv-icon">&#9679;</span> {{ $t('cvAts.contact.location') }}</li>
            <li><span class="cv-icon">&#9679;</span> github.com/JaredSoftware</li>
            <li><span class="cv-icon">&#9679;</span> linkedin.com/in/jared-wesley-vargas-cortes-0ab9a71a8</li>
          </ul>
        </div>

        <!-- Stack Técnico -->
        <div class="cv-sidebar-section">
          <h3 class="cv-sidebar-title">{{ $t('skills.coreStack') }}</h3>
          <div class="cv-skill-row"><span class="cv-skill-name">Node.js</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">NestJS</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9675;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">APIs REST</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">PostgreSQL</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9675;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">MongoDB</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9675;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">Docker</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9675;&#9675;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">Linux / PM2</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9675;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">Git</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">Vue / Nuxt</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9675;&#9675;</span></div>
        </div>

        <!-- Habilidades Blandas -->
        <div class="cv-sidebar-section">
          <h3 class="cv-sidebar-title">{{ $t('skills.softSkills') }}</h3>
          <div class="cv-skill-row"><span class="cv-skill-name">{{ $t('softSkills.agileDelivery') }}</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">{{ $t('softSkills.technicalLeadership') }}</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9675;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">{{ $t('softSkills.solutionOriented') }}</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">{{ $t('softSkills.inventiveThinking') }}</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
        </div>

        <!-- Idiomas -->
        <div class="cv-sidebar-section">
          <h3 class="cv-sidebar-title">{{ $t('sections.languages') }}</h3>
          <div class="cv-skill-row"><span class="cv-skill-name">Español</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9679;</span></div>
          <div class="cv-skill-row"><span class="cv-skill-name">{{ $t('languages.english') }}</span><span class="cv-dots">&#9679;&#9679;&#9679;&#9679;&#9675;</span></div>
        </div>

        <!-- Hobbies -->
        <div class="cv-sidebar-section">
          <h3 class="cv-sidebar-title">{{ $t('sections.hobbies') }}</h3>
          <p class="cv-sidebar-text">{{ $t('hobbies.music') }}</p>
          <p class="cv-sidebar-text">{{ $t('hobbies.learning') }}</p>
        </div>

      </aside>

      <!-- MAIN CONTENT -->
      <main class="cv-main">

        <!-- Header -->
        <div class="cv-main-header">
          <h1 class="cv-name">{{ $t('name') }}</h1>
          <p class="cv-job-title">{{ $t('title') }}</p>
        </div>

        <!-- Perfil Profesional -->
        <section class="cv-section">
          <h2 class="cv-section-title">{{ $t('sections.professionalProfile') }}</h2>
          <p class="cv-text">{{ $t('profile.intro') }}</p>

          <p class="cv-subsection-title">{{ $t('profile.backend.title') }}</p>
          <ul class="cv-list">
            <li v-for="(point, index) in backendPoints" :key="'be-' + index">{{ point }}</li>
          </ul>

          <p class="cv-subsection-title">{{ $t('profile.tools.title') }}</p>
          <ul class="cv-list">
            <li v-for="(point, index) in toolsPoints" :key="'tools-' + index">{{ point }}</li>
          </ul>
        </section>

        <!-- Experiencia Laboral -->
        <section class="cv-section">
          <h2 class="cv-section-title">{{ $t('sections.workExperience') }}</h2>

          <div class="cv-job">
            <div class="cv-job-top">
              <div>
                <div class="cv-job-position">{{ $t('experience.bercontCurrent.position') }}</div>
                <div class="cv-job-company">{{ $t('experience.bercontCurrent.company') }}</div>
              </div>
              <div class="cv-job-date">{{ $t('experience.bercontCurrent.period') }}</div>
            </div>
            <ul class="cv-list">
              <li v-for="(point, index) in bercontCurrentPoints" :key="index">{{ point }}</li>
            </ul>
          </div>

          <div class="cv-job">
            <div class="cv-job-top">
              <div>
                <div class="cv-job-position">{{ $t('experience.bercont.position') }}</div>
                <div class="cv-job-company">{{ $t('experience.bercont.company') }}</div>
              </div>
              <div class="cv-job-date">{{ $t('experience.bercont.period') }}</div>
            </div>
            <ul class="cv-list">
              <li v-for="(point, index) in bercontDeveloperPoints" :key="index">{{ point }}</li>
            </ul>
          </div>

          <div class="cv-job">
            <div class="cv-job-top">
              <div>
                <div class="cv-job-position">{{ $t('sections.internships') }}</div>
                <div class="cv-job-company">{{ $t('internships.bercont.company') }}</div>
              </div>
              <div class="cv-job-date">{{ $t('internships.bercont.period') }}</div>
            </div>
            <ul class="cv-list">
              <li v-for="(point, index) in internshipPoints" :key="index">{{ point }}</li>
            </ul>
          </div>
        </section>

        <!-- Proyectos -->
        <section class="cv-section">
          <h2 class="cv-section-title">{{ $t('sections.projects') }}</h2>
          <div class="cv-job">
            <div class="cv-job-top">
              <div>
                <div class="cv-job-position">{{ $t('projects.assistme.name') }}</div>
                <div class="cv-job-company">{{ $t('projects.assistme.role') }} · myassist-me.com</div>
              </div>
            </div>
            <p class="cv-text" style="margin-top: 5px;">{{ $t('projects.assistme.description') }}</p>
          </div>
        </section>

        <!-- Formación -->
        <section class="cv-section">
          <h2 class="cv-section-title">{{ $t('sections.education') }}</h2>
          <div class="cv-job">
            <div class="cv-job-top">
              <div class="cv-job-position">{{ $t('education.technician.degree') }}</div>
              <div class="cv-job-date">{{ $t('education.technician.period') }}</div>
            </div>
            <div class="cv-job-company">{{ $t('education.technician.description') }}</div>
          </div>
          <div class="cv-job">
            <div class="cv-job-top">
              <div class="cv-job-position">{{ $t('education.highschool.degree') }}</div>
              <div class="cv-job-date">{{ $t('education.highschool.period') }}</div>
            </div>
            <div class="cv-job-company">{{ $t('education.highschool.description') }}</div>
          </div>
        </section>

      </main>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t, tm, messages, locale } = useI18n()
const router = useRouter()

const basePath = computed(() => {
  const base = router.options.history?.base || '/cv/'
  return base.replace(/\/$/, '') + '/'
})
const profileImagePath = computed(() => `${basePath.value}jared.jpeg?v=20260421`)

// Función para extraer strings de arrays de traducciones
const extractStrings = (items) => {
  if (!items || !Array.isArray(items)) {
    return []
  }
  
  return items.map(item => {
    if (typeof item === 'string') {
      return item
    }
    if (item && typeof item === 'object') {
      if (item.body && typeof item.body === 'object' && typeof item.body.static === 'string') {
        return item.body.static
      }
      if (typeof item.static === 'string') {
        return item.static
      }
      if (typeof item.source === 'string') {
        return item.source
      }
    }
    return ''
  }).filter(item => item && typeof item === 'string' && item.length > 0)
}

// Función para obtener arrays de traducciones
const getTranslationArray = (key) => {
  try {
    const result = tm(key)
    if (Array.isArray(result)) {
      return extractStrings(result)
    }
    const localeMessages = messages.value?.[locale.value]
    if (localeMessages) {
      const keys = key.split('.')
      let value = localeMessages
      for (const k of keys) {
        if (value && typeof value === 'object' && k in value) {
          value = value[k]
        } else {
          value = null
          break
        }
      }
      if (Array.isArray(value)) {
        return extractStrings(value)
      }
    }
    return []
  } catch (e) {
    console.error('Error loading translation:', key, e)
    return []
  }
}

const bercontCurrentPoints = computed(() => getTranslationArray('cvAts.workExperience.bercontCurrent.points'))
const bercontDeveloperPoints = computed(() => getTranslationArray('cvAts.workExperience.bercontDeveloper.points'))
const internshipPoints = computed(() => getTranslationArray('cvAts.workExperience.internship.points'))

const backendPoints = computed(() => getTranslationArray('profile.backend.points'))
const toolsPoints = computed(() => getTranslationArray('profile.tools.points'))
const frontendPoints = computed(() => getTranslationArray('profile.frontend.points'))

const downloadPDF = () => {
  if (!process.client) return

  const button = document.getElementById('pdf-button')
  if (button) button.style.display = 'none'

  const restoreButton = () => {
    if (button) button.style.display = ''
    window.removeEventListener('afterprint', restoreButton)
  }
  window.addEventListener('afterprint', restoreButton)

  window.print()
}
</script>

<style>
/* ── Wrapper ── */
.cv-wrapper {
  display: flex;
  width: 210mm;
  min-height: 297mm;
  margin: 0 auto;
  font-family: 'Arial', 'Helvetica', sans-serif;
  font-size: 9.5pt;
  line-height: 1.4;
  color: #1f2937;
  background: #fff;
  box-sizing: border-box;
}

/* ── SIDEBAR ── */
.cv-sidebar {
  width: 34%;
  min-width: 34%;
  background: #1a3a5c;
  color: #e2e8f0;
  padding: 20px 14px;
  box-sizing: border-box;
}

.cv-photo-wrap {
  display: flex;
  justify-content: center;
  margin-bottom: 14px;
}

.cv-photo {
  width: 90px;
  height: 90px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid #3b82f6;
}

.cv-sidebar-name {
  font-size: 11pt;
  font-weight: bold;
  color: #fff;
  text-align: center;
  margin-bottom: 16px;
  line-height: 1.3;
}

.cv-sidebar-section {
  margin-bottom: 16px;
}

.cv-sidebar-title {
  font-size: 8.5pt;
  font-weight: bold;
  color: #93c5fd;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  border-bottom: 1px solid #2d5a8a;
  padding-bottom: 4px;
  margin: 0 0 8px 0;
}

.cv-contact-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.cv-contact-list li {
  font-size: 8pt;
  color: #cbd5e1;
  margin-bottom: 4px;
  word-break: break-all;
  line-height: 1.3;
}

.cv-icon {
  margin-right: 4px;
  color: #60a5fa;
}

.cv-skill-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 5px;
}

.cv-skill-name {
  font-size: 8.5pt;
  color: #e2e8f0;
  flex: 1;
  padding-right: 6px;
}

.cv-dots {
  font-size: 8pt;
  color: #60a5fa;
  letter-spacing: 2px;
  white-space: nowrap;
}

.cv-sidebar-text {
  font-size: 8pt;
  color: #cbd5e1;
  margin: 0 0 4px 0;
  line-height: 1.3;
}

/* ── MAIN ── */
.cv-main {
  width: 66%;
  padding: 20px 18px;
  box-sizing: border-box;
}

.cv-main-header {
  border-bottom: 3px solid #2563eb;
  padding-bottom: 10px;
  margin-bottom: 14px;
}

.cv-name {
  font-size: 20pt;
  font-weight: bold;
  color: #1e3a5f;
  margin: 0 0 3px 0;
  line-height: 1.1;
}

.cv-job-title {
  font-size: 10pt;
  font-weight: 600;
  color: #2563eb;
  margin: 0;
}

.cv-section {
  margin-bottom: 14px;
}

.cv-section-title {
  font-size: 10pt;
  font-weight: bold;
  color: #1e3a5f;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  border-left: 4px solid #2563eb;
  padding-left: 7px;
  margin: 0 0 8px 0;
}

.cv-text {
  font-size: 8.5pt;
  color: #374151;
  line-height: 1.5;
  margin: 0 0 6px 0;
  text-align: justify;
}

.cv-subsection-title {
  font-size: 9pt;
  font-weight: bold;
  color: #1e3a5f;
  margin: 8px 0 3px 0;
}

.cv-list {
  margin: 4px 0 0 16px;
  padding: 0;
  list-style-type: disc;
}

.cv-list li {
  font-size: 8.5pt;
  color: #374151;
  margin-bottom: 3px;
  line-height: 1.4;
}

.cv-job {
  margin-bottom: 10px;
  page-break-inside: avoid;
}

.cv-job-top {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 2px;
}

.cv-job-position {
  font-size: 9.5pt;
  font-weight: bold;
  color: #1f2937;
}

.cv-job-company {
  font-size: 8.5pt;
  color: #6b7280;
  margin-bottom: 4px;
}

.cv-job-date {
  font-size: 8pt;
  color: #2563eb;
  white-space: nowrap;
  margin-left: 8px;
  font-style: italic;
}

/* ── Print / PDF ── */
@media print {
  * {
    -webkit-print-color-adjust: exact !important;
    print-color-adjust: exact !important;
  }
  html, body {
    margin: 0 !important;
    padding: 0 !important;
    background: white !important;
  }
  #pdf-button { display: none !important; }
  .cv-wrapper {
    box-shadow: none !important;
    margin: 0 !important;
    width: 100% !important;
  }
}

@page {
  size: A4 portrait;
  margin: 0;
}

@media screen {
  body { background: #e5e7eb; margin: 0; padding: 20px; }
  .cv-wrapper {
    box-shadow: 0 4px 24px rgba(0,0,0,0.18);
    margin: 20px auto;
  }
}
</style>
