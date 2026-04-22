<template>
  <div class="font-display bg-background-light dark:bg-background-dark text-text-primary-light dark:text-text-primary-dark antialiased min-h-screen">

    <!-- Toast -->
    <Transition name="toast">
      <div
        v-if="toast.visible"
        class="fixed top-6 right-6 z-50 flex items-center gap-3 px-5 py-3.5 rounded-xl shadow-lg text-white text-sm font-semibold"
        :class="toast.type === 'success' ? 'bg-green-500' : 'bg-red-500'"
      >
        <span class="material-symbols-outlined !text-lg">{{ toast.type === 'success' ? 'check_circle' : 'error' }}</span>
        {{ toast.message }}
      </div>
    </Transition>

    <div class="relative flex h-auto min-h-screen w-full flex-col">
      <!-- Parallax orbs — siguen el mouse a distintas velocidades -->
      <div class="parallax-scene" aria-hidden="true">
        <div class="p-orb p-orb-1" data-speed="0.04"></div>
        <div class="p-orb p-orb-2" data-speed="0.07"></div>
        <div class="p-orb p-orb-3" data-speed="0.02"></div>
        <div class="p-orb p-orb-4" data-speed="0.09"></div>
        <div class="p-orb p-orb-5" data-speed="0.05"></div>
      </div>

      <div class="layout-container flex h-full grow flex-col">
        <main class="flex-1 py-12 md:py-20">
          <div class="mx-auto flex max-w-6xl flex-col gap-12 px-4">

            <header class="flex flex-col items-center gap-8 md:gap-12 relative">

              <!-- Canvas 3D -->
              <div class="w-full max-w-md">
                <Transition name="model-fade">
                  <div
                    v-if="!modelMounted"
                    key="skeleton"
                    class="w-full h-64 md:h-80 rounded-2xl skeleton-shimmer"
                  ></div>
                  <Model3D
                    v-else
                    key="model"
                    :model-path="modelPath"
                    :auto-rotate="true"
                    :rotation-speed="0.005"
                    class="w-full"
                  />
                </Transition>
              </div>

              <!-- Foto + texto -->
              <div class="flex flex-col md:flex-row items-center md:items-center justify-center gap-8 md:gap-12 w-full">

                <!-- Avatar con anillo giratorio -->
                <div class="relative flex-shrink-0 hero-avatar">
                  <!-- Anillo de gradiente giratorio -->
                  <div class="avatar-ring-outer">
                    <div class="avatar-ring-inner"></div>
                  </div>
                  <!-- Skeleton -->
                  <div
                    v-show="!profileImageLoaded"
                    class="absolute inset-[4px] rounded-full skeleton-shimmer z-10"
                  ></div>
                  <!-- Imagen -->
                  <div
                    class="relative z-10 h-32 w-32 md:h-40 md:w-40 rounded-full bg-center bg-no-repeat bg-cover transition-opacity duration-500"
                    :class="profileImageLoaded ? 'opacity-100' : 'opacity-0'"
                    :style="{ backgroundImage: `url('${profileImagePath}')` }"
                  ></div>
                </div>

                <!-- Texto -->
                <div class="flex flex-col text-center md:text-left items-center md:items-start gap-4 w-full md:w-auto hero-info">
                  <div>
                    <h1 class="text-text-primary-light dark:text-white text-4xl md:text-5xl font-extrabold tracking-tighter">
                      {{ $t('name') }}
                    </h1>
                    <!-- Typewriter en el título -->
                    <p class="text-primary dark:text-blue-400 text-lg md:text-xl font-medium mt-1 typewriter-wrap">
                      <span class="typewriter">{{ $t('title') }}</span>
                    </p>
                  </div>
                  <p class="text-text-secondary-light dark:text-text-secondary-dark max-w-xl text-base md:text-lg">
                    {{ $t('description') }}
                  </p>
                  <button
                    class="group relative flex min-w-[84px] cursor-pointer items-center justify-center overflow-hidden rounded-xl h-11 px-6 btn-glow text-white text-base font-bold leading-normal tracking-wide transition-all duration-300 ease-in-out active:scale-95 disabled:opacity-70 disabled:cursor-not-allowed"
                    :disabled="isDownloading"
                    @click="downloadCV"
                  >
                    <span v-if="!isDownloading" class="material-symbols-outlined !text-xl transition-transform duration-300 group-hover:-translate-y-1.5">download</span>
                    <span v-else class="btn-spinner"></span>
                    <span class="ml-2 truncate transition-transform duration-300 group-hover:-translate-y-1.5">
                      {{ isDownloading ? $t('downloadCV') + '...' : $t('downloadCV') }}
                    </span>
                  </button>
                </div>
              </div>

            </header>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-12">
              <!-- Main Content -->
              <div class="lg:col-span-2 flex flex-col gap-10">

                <!-- Professional Summary -->
                <section class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft p-6 md:p-8 reveal tilt-card glass-card">
                  <h2 class="text-text-primary-light dark:text-white text-2xl font-bold tracking-tight mb-4 flex items-center gap-3">
                    <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-3xl">person</span>
                    {{ $t('sections.professionalProfile') }}
                  </h2>
                  <p class="text-text-secondary-light dark:text-text-secondary-dark text-base leading-relaxed mb-4">
                    {{ $t('profile.intro') }}
                  </p>
                  <h3 class="text-lg font-semibold text-text-primary-light dark:text-white mt-4 mb-2">
                    {{ $t('profile.backend.title') }}
                  </h3>
                  <ul class="list-disc pl-5 text-text-secondary-light dark:text-text-secondary-dark text-sm space-y-2 mb-4">
                    <li v-for="(point, index) in backendPoints" :key="index">{{ point }}</li>
                  </ul>
                  <h3 class="text-lg font-semibold text-text-primary-light dark:text-white mt-4 mb-2">
                    {{ $t('profile.tools.title') }}
                  </h3>
                  <ul class="list-disc pl-5 text-text-secondary-light dark:text-text-secondary-dark text-sm space-y-2 mb-4">
                    <li v-for="(point, index) in toolsPoints" :key="index">{{ point }}</li>
                  </ul>
                  <h3 class="text-lg font-semibold text-text-primary-light dark:text-white mt-4 mb-2">
                    {{ $t('profile.frontend.title') }}
                  </h3>
                  <ul class="list-disc pl-5 text-text-secondary-light dark:text-text-secondary-dark text-sm space-y-2">
                    <li v-for="(point, index) in frontendPoints" :key="index">{{ point }}</li>
                  </ul>
                </section>

                <!-- Work Experience — timeline -->
                <section class="reveal">
                  <h2 class="text-text-primary-light dark:text-white text-2xl font-bold tracking-tight mb-6 flex items-center gap-3">
                    <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-3xl">work</span>
                    {{ $t('sections.workExperience') }}
                  </h2>
                  <div class="relative pl-8 border-l-2 border-blue-200 dark:border-blue-900 space-y-10">
                    <div class="relative">
                      <div class="absolute -left-[2.65rem] top-1 w-5 h-5 rounded-full bg-blue-500 border-4 border-background-light dark:border-background-dark ring-2 ring-blue-500/40"></div>
                      <div class="flex flex-col sm:flex-row justify-between sm:items-center">
                        <h3 class="text-lg font-semibold text-text-primary-light dark:text-white">{{ $t('experience.bercontCurrent.position') }}</h3>
                        <span class="inline-flex items-center gap-1 text-xs font-medium text-blue-600 dark:text-blue-400 bg-blue-100 dark:bg-blue-900/50 px-2.5 py-1 rounded-full mt-1 sm:mt-0 w-fit">
                          <span class="w-1.5 h-1.5 rounded-full bg-blue-500 animate-pulse"></span>
                          {{ $t('experience.bercontCurrent.period') }}
                        </span>
                      </div>
                      <p class="text-sm font-medium text-primary dark:text-blue-400 mt-1">{{ $t('experience.bercontCurrent.company') }}</p>
                      <ul class="list-disc pl-5 mt-3 text-text-secondary-light dark:text-text-secondary-dark text-sm space-y-2">
                        <li>{{ $t('experience.bercontCurrent.description') }}</li>
                      </ul>
                    </div>
                    <div class="relative">
                      <div class="absolute -left-[2.65rem] top-1 w-5 h-5 rounded-full bg-gray-300 dark:bg-gray-600 border-4 border-background-light dark:border-background-dark"></div>
                      <div class="flex flex-col sm:flex-row justify-between sm:items-center">
                        <h3 class="text-lg font-semibold text-text-primary-light dark:text-white">{{ $t('experience.bercont.position') }}</h3>
                        <span class="text-xs font-medium text-text-secondary-light dark:text-text-secondary-dark bg-border-light dark:bg-border-dark px-2.5 py-1 rounded-full mt-1 sm:mt-0 w-fit">{{ $t('experience.bercont.period') }}</span>
                      </div>
                      <p class="text-sm font-medium text-text-secondary-light dark:text-text-secondary-dark mt-1">{{ $t('experience.bercont.company') }}</p>
                      <ul class="list-disc pl-5 mt-3 text-text-secondary-light dark:text-text-secondary-dark text-sm space-y-2">
                        <li>{{ $t('experience.bercont.description') }}</li>
                      </ul>
                    </div>
                  </div>
                </section>

                <!-- GitHub Contributions -->
                <div class="reveal">
                  <GitHubContributions username="JaredSoftware" />
                </div>

                <!-- Projects -->
                <section class="reveal">
                  <h2 class="text-text-primary-light dark:text-white text-2xl font-bold tracking-tight mb-6 flex items-center gap-3">
                    <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-3xl">lightbulb</span>
                    {{ $t('sections.projects') }}
                  </h2>
                  <div class="flex justify-center">
                    <div class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft overflow-hidden group w-full max-w-2xl hover:shadow-xl transition-shadow duration-300">
                      <a href="https://www.myassist-me.com/" target="_blank" rel="noopener noreferrer" class="block">
                        <div class="w-full h-48 bg-gradient-to-br from-blue-600 to-blue-800 flex items-center justify-center overflow-hidden">
                          <div class="text-center text-white px-4 transition-transform duration-500 group-hover:scale-110">
                            <div class="text-4xl font-bold mb-2">ASSIST-ME</div>
                            <div class="text-sm opacity-90">Virtual Receptionist Platform</div>
                          </div>
                        </div>
                        <div class="p-4">
                          <h3 class="text-lg font-semibold text-text-primary-light dark:text-white group-hover:text-blue-500 transition-colors duration-200">{{ $t('projects.assistme.name') }}</h3>
                          <p class="text-sm text-text-secondary-light dark:text-text-secondary-dark mt-1">{{ $t('projects.assistme.role') }}</p>
                          <p class="text-sm text-text-secondary-light dark:text-text-secondary-dark mt-2">{{ $t('projects.assistme.description') }}</p>
                          <div class="flex flex-wrap gap-2 mt-3">
                            <span class="bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300 text-xs font-medium px-2.5 py-0.5 rounded-full">Figma</span>
                            <span class="bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300 text-xs font-medium px-2.5 py-0.5 rounded-full">Node.js</span>
                            <span class="bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300 text-xs font-medium px-2.5 py-0.5 rounded-full">Microservicios</span>
                            <span class="bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300 text-xs font-medium px-2.5 py-0.5 rounded-full">API REST</span>
                          </div>
                        </div>
                      </a>
                    </div>
                  </div>
                </section>

                <!-- Education — timeline -->
                <section class="reveal">
                  <h2 class="text-text-primary-light dark:text-white text-2xl font-bold tracking-tight mb-6 flex items-center gap-3">
                    <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-3xl">school</span>
                    {{ $t('sections.education') }}
                  </h2>
                  <div class="relative pl-8 border-l-2 border-blue-200 dark:border-blue-900 space-y-10">
                    <div class="relative">
                      <div class="absolute -left-[2.65rem] top-1 w-5 h-5 rounded-full bg-blue-500 border-4 border-background-light dark:border-background-dark"></div>
                      <div class="flex flex-col sm:flex-row justify-between sm:items-center">
                        <h3 class="text-lg font-semibold text-text-primary-light dark:text-white">{{ $t('education.technician.degree') }}</h3>
                        <span class="text-xs font-medium text-text-secondary-light dark:text-text-secondary-dark bg-border-light dark:bg-border-dark px-2.5 py-1 rounded-full mt-1 sm:mt-0 w-fit">{{ $t('education.technician.period') }}</span>
                      </div>
                      <p class="text-base text-text-secondary-light dark:text-text-secondary-dark mt-1">{{ $t('education.technician.description') }}</p>
                    </div>
                    <div class="relative">
                      <div class="absolute -left-[2.65rem] top-1 w-5 h-5 rounded-full bg-gray-300 dark:bg-gray-600 border-4 border-background-light dark:border-background-dark"></div>
                      <div class="flex flex-col sm:flex-row justify-between sm:items-center">
                        <h3 class="text-lg font-semibold text-text-primary-light dark:text-white">{{ $t('education.highschool.degree') }}</h3>
                        <span class="text-xs font-medium text-text-secondary-light dark:text-text-secondary-dark bg-border-light dark:bg-border-dark px-2.5 py-1 rounded-full mt-1 sm:mt-0 w-fit">{{ $t('education.highschool.period') }}</span>
                      </div>
                      <p class="text-base text-text-secondary-light dark:text-text-secondary-dark mt-1">{{ $t('education.highschool.description') }}</p>
                    </div>
                  </div>
                </section>

                <!-- Internships — timeline -->
                <section class="reveal">
                  <h2 class="text-text-primary-light dark:text-white text-2xl font-bold tracking-tight mb-6 flex items-center gap-3">
                    <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-3xl">work_history</span>
                    {{ $t('sections.internships') }}
                  </h2>
                  <div class="relative pl-8 border-l-2 border-blue-200 dark:border-blue-900 space-y-10">
                    <div class="relative">
                      <div class="absolute -left-[2.65rem] top-1 w-5 h-5 rounded-full bg-gray-300 dark:bg-gray-600 border-4 border-background-light dark:border-background-dark"></div>
                      <div class="flex flex-col sm:flex-row justify-between sm:items-center">
                        <h3 class="text-lg font-semibold text-text-primary-light dark:text-white">{{ $t('internships.bercont.company') }}</h3>
                        <span class="text-xs font-medium text-text-secondary-light dark:text-text-secondary-dark bg-border-light dark:bg-border-dark px-2.5 py-1 rounded-full mt-1 sm:mt-0 w-fit">{{ $t('internships.bercont.period') }}</span>
                      </div>
                      <p class="text-base text-text-secondary-light dark:text-text-secondary-dark mt-1">{{ $t('internships.bercont.description') }}</p>
                    </div>
                  </div>
                </section>
              </div>

              <!-- Sidebar -->
              <div class="lg:col-span-1 flex flex-col gap-10">

                <!-- Contact -->
                <section class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft p-6 reveal tilt-card glass-card">
                  <h3 class="text-lg font-bold text-text-primary-light dark:text-white mb-4">{{ $t('sections.contactInfo') }}</h3>
                  <ul class="space-y-4 text-sm">
                    <li class="flex items-center gap-3 group/item">
                      <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-xl transition-transform duration-200 group-hover/item:scale-110">mail</span>
                      <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400 transition-colors" href="mailto:jaredwesley27@hotmail.com">{{ $t('contact.email') }}</a>
                    </li>
                    <li class="flex items-center gap-3 group/item">
                      <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-xl transition-transform duration-200 group-hover/item:scale-110">phone</span>
                      <span class="text-text-secondary-light dark:text-text-secondary-dark">{{ $t('contact.phone') }}</span>
                    </li>
                    <li class="flex items-center gap-3 group/item">
                      <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-xl transition-transform duration-200 group-hover/item:scale-110">location_on</span>
                      <span class="text-text-secondary-light dark:text-text-secondary-dark">{{ $t('contact.location') }}</span>
                    </li>
                    <li class="flex items-center gap-3 group/item">
                      <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-xl transition-transform duration-200 group-hover/item:scale-110">link</span>
                      <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400" href="https://lollool2.github.io/-CV.com/" target="_blank">{{ $t('contact.website') }}</a>
                    </li>
                    <li class="flex items-center gap-3 group/item">
                      <svg aria-hidden="true" class="w-5 h-5 text-primary dark:text-blue-400 transition-transform duration-200 group-hover/item:scale-110" fill="currentColor" viewBox="0 0 24 24">
                        <path clip-rule="evenodd" d="M12 2C6.477 2 2 6.477 2 12c0 4.418 2.865 8.168 6.839 9.492.5.092.682-.217.682-.482 0-.237-.009-.868-.014-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.031-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.203 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.338 4.695-4.566 4.942.359.308.678.92.678 1.852 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.001 10.001 0 0022 12c0-5.523-4.477-10-10-10z" fill-rule="evenodd"></path>
                      </svg>
                      <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400" href="https://github.com/JaredSoftware" target="_blank">{{ $t('contact.github') }}</a>
                    </li>
                    <li class="flex items-center gap-3 group/item">
                      <svg aria-hidden="true" class="w-5 h-5 text-primary dark:text-blue-400 transition-transform duration-200 group-hover/item:scale-110" fill="currentColor" viewBox="0 0 24 24">
                        <path d="M19 0h-14c-2.761 0-5 2.239-5 5v14c0 2.761 2.239 5 5 5h14c2.762 0 5-2.239 5-5v-14c0-2.761-2.238-5-5-5zm-11 19h-3v-11h3v11zm-1.5-12.268c-.966 0-1.75-.79-1.75-1.764s.784-1.764 1.75-1.764 1.75.79 1.75 1.764-.783 1.764-1.75 1.764zm13.5 12.268h-3v-5.604c0-3.368-4-3.113-4 0v5.604h-3v-11h3v1.765c1.396-2.586 7-2.777 7 2.476v6.759z"></path>
                      </svg>
                      <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400" href="https://www.linkedin.com/in/jared-wesley-vargas-cortes-0ab9a71a8" target="_blank">{{ $t('contact.linkedin') }}</a>
                    </li>
                  </ul>
                </section>

                <!-- Skills -->
                <section class="reveal">
                  <h2 class="text-text-primary-light dark:text-white text-2xl font-bold tracking-tight mb-6 flex items-center gap-3">
                    <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-3xl">psychology</span>
                    {{ $t('sections.skills') }}
                  </h2>
                  <div class="space-y-6">
                    <div class="bg-blue-50 dark:bg-blue-950 border border-blue-200 dark:border-blue-800 rounded-xl p-4">
                      <h4 class="font-semibold text-blue-700 dark:text-blue-300 mb-4 flex items-center gap-2">
                        <span class="material-symbols-outlined !text-lg">groups</span>
                        {{ $t('skills.softSkills') }}
                      </h4>
                      <div class="grid grid-cols-1 gap-3">
                        <div class="flex items-start gap-3 bg-white dark:bg-blue-900 border border-blue-200 dark:border-blue-700 rounded-lg px-3 py-2.5 hover:border-blue-400 dark:hover:border-blue-500 transition-colors duration-200">
                          <span class="material-symbols-outlined text-blue-500 dark:text-blue-300 !text-xl mt-0.5 flex-shrink-0">rocket_launch</span>
                          <div>
                            <p class="text-sm font-semibold text-blue-800 dark:text-blue-200">{{ $t('softSkills.agileDelivery') }}</p>
                            <p class="text-xs text-blue-600 dark:text-blue-400 mt-0.5">{{ $t('softSkills.agileDeliveryDesc') }}</p>
                          </div>
                        </div>
                        <div class="flex items-start gap-3 bg-white dark:bg-blue-900 border border-blue-200 dark:border-blue-700 rounded-lg px-3 py-2.5 hover:border-blue-400 dark:hover:border-blue-500 transition-colors duration-200">
                          <span class="material-symbols-outlined text-blue-500 dark:text-blue-300 !text-xl mt-0.5 flex-shrink-0">supervisor_account</span>
                          <div>
                            <p class="text-sm font-semibold text-blue-800 dark:text-blue-200">{{ $t('softSkills.technicalLeadership') }}</p>
                            <p class="text-xs text-blue-600 dark:text-blue-400 mt-0.5">{{ $t('softSkills.technicalLeadershipDesc') }}</p>
                          </div>
                        </div>
                        <div class="flex items-start gap-3 bg-white dark:bg-blue-900 border border-blue-200 dark:border-blue-700 rounded-lg px-3 py-2.5 hover:border-blue-400 dark:hover:border-blue-500 transition-colors duration-200">
                          <span class="material-symbols-outlined text-blue-500 dark:text-blue-300 !text-xl mt-0.5 flex-shrink-0">target</span>
                          <div>
                            <p class="text-sm font-semibold text-blue-800 dark:text-blue-200">{{ $t('softSkills.solutionOriented') }}</p>
                            <p class="text-xs text-blue-600 dark:text-blue-400 mt-0.5">{{ $t('softSkills.solutionOrientedDesc') }}</p>
                          </div>
                        </div>
                        <div class="flex items-start gap-3 bg-white dark:bg-blue-900 border border-blue-200 dark:border-blue-700 rounded-lg px-3 py-2.5 hover:border-blue-400 dark:hover:border-blue-500 transition-colors duration-200">
                          <span class="material-symbols-outlined text-blue-500 dark:text-blue-300 !text-xl mt-0.5 flex-shrink-0">explore</span>
                          <div>
                            <p class="text-sm font-semibold text-blue-800 dark:text-blue-200">{{ $t('softSkills.inventiveThinking') }}</p>
                            <p class="text-xs text-blue-600 dark:text-blue-400 mt-0.5">{{ $t('softSkills.inventiveThinkingDesc') }}</p>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft p-4">
                      <h4 class="font-semibold text-text-primary-light dark:text-white mb-3 flex items-center gap-2">
                        <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-lg">terminal</span>
                        {{ $t('skills.coreStack') }}
                      </h4>
                      <div class="flex flex-wrap gap-2">
                        <span v-for="tag in ['Node.js','NestJS','Express','APIs REST','PostgreSQL','MongoDB','Microservicios']" :key="tag"
                          class="bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300 text-sm font-medium px-3 py-1.5 rounded-lg hover:bg-blue-100 hover:text-blue-800 dark:hover:bg-blue-900 dark:hover:text-blue-300 transition-colors duration-200 cursor-default">
                          {{ tag }}
                        </span>
                      </div>
                    </div>
                    <div class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft p-4">
                      <h4 class="font-semibold text-text-primary-light dark:text-white mb-3 flex items-center gap-2">
                        <span class="material-symbols-outlined text-primary dark:text-blue-400 !text-lg">dns</span>
                        {{ $t('skills.infraTools') }}
                      </h4>
                      <div class="flex flex-wrap gap-2">
                        <span v-for="tag in ['Docker','Linux','PM2','Git','WebSockets']" :key="tag"
                          class="bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300 text-sm font-medium px-3 py-1.5 rounded-lg hover:bg-blue-100 hover:text-blue-800 dark:hover:bg-blue-900 dark:hover:text-blue-300 transition-colors duration-200 cursor-default">
                          {{ tag }}
                        </span>
                      </div>
                    </div>
                  </div>
                </section>

                <!-- Languages -->
                <section class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft p-6 reveal tilt-card glass-card">
                  <h3 class="text-lg font-bold text-text-primary-light dark:text-white mb-4">{{ $t('sections.languages') }}</h3>
                  <div class="space-y-4">
                    <div>
                      <div class="flex justify-between mb-2">
                        <span class="text-sm font-medium text-text-primary-light dark:text-white">{{ $t('languages.english') }}</span>
                        <span class="text-sm text-text-secondary-light dark:text-text-secondary-dark">{{ $t('languages.level') }}</span>
                      </div>
                      <div class="w-full bg-gray-200 rounded-full h-2.5 dark:bg-gray-700 overflow-hidden">
                        <div
                          class="bg-blue-600 h-2.5 rounded-full dark:bg-blue-500 transition-all duration-1000 ease-out"
                          :style="{ width: langBarVisible ? '75%' : '0%' }"
                          ref="langBarRef"
                        ></div>
                      </div>
                    </div>
                  </div>
                </section>

                <!-- Hobbies -->
                <section class="bg-card-light dark:bg-card-dark rounded-xl shadow-soft p-6 reveal tilt-card glass-card">
                  <h3 class="text-lg font-bold text-text-primary-light dark:text-white mb-4">{{ $t('sections.hobbies') }}</h3>
                  <div class="flex flex-wrap gap-2">
                    <span class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-full bg-blue-500 text-white text-sm font-medium">
                      <span class="material-symbols-outlined !text-base">music_note</span>
                      {{ $t('hobbies.music') }}
                    </span>
                    <span class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-full bg-border-light dark:bg-border-dark text-text-primary-light dark:text-text-primary-dark text-sm font-medium hover:bg-blue-100 dark:hover:bg-blue-900/50 hover:text-blue-700 dark:hover:text-blue-300 transition-colors duration-200 cursor-default">
                      <span class="material-symbols-outlined !text-base">auto_stories</span>
                      {{ $t('hobbies.learning') }}
                    </span>
                  </div>
                </section>

              </div>
            </div>
          </div>
        </main>

        <footer class="bg-card-light dark:bg-card-dark border-t border-border-light dark:border-border-dark mt-12">
          <div class="mx-auto max-w-6xl px-4 py-8">
            <div class="flex flex-col sm:flex-row justify-between items-center gap-6">
              <p class="text-sm text-text-secondary-light dark:text-text-secondary-dark">{{ $t('footer.rights') }}</p>
              <div class="flex items-center gap-4">
                <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400 transition-colors" href="https://lollool2.github.io/-CV.com/" target="_blank">{{ $t('footer.portfolio') }}</a>
                <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400 transition-colors" href="https://www.linkedin.com/in/jared-wesley-vargas-cortes-0ab9a71a8" target="_blank">{{ $t('footer.linkedin') }}</a>
                <a class="text-text-secondary-light dark:text-text-secondary-dark hover:text-primary dark:hover:text-blue-400 transition-colors" href="mailto:jaredwesley27@hotmail.com">{{ $t('footer.contact') }}</a>
              </div>
            </div>
          </div>
        </footer>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, onMounted, onUnmounted, nextTick } from 'vue'

const { locale, tm, messages } = useI18n()
const router = useRouter()

// ── Paths ──────────────────────────────────────────────────────────────────────
const basePath = computed(() => {
  const base = router.options.history?.base || '/cv/'
  return base.replace(/\/$/, '') + '/'
})
const modelPath = computed(() => `${basePath.value}robot-optimized.glb`)
const profileImagePath = computed(() => `${basePath.value}jared.jpeg?v=20260421`)

// ── Defer 3D — Three.js se monta solo cuando el browser está idle ──────────────
// Esto evita que el parser/compilador de shaders de WebGL compita con
// el primer paint del HTML y la descarga de la imagen de perfil.
const modelMounted = ref(false)

// ── Foto de perfil ─────────────────────────────────────────────────────────────
const profileImageLoaded = ref(false)

// ── Parallax ───────────────────────────────────────────────────────────────────
const heroRef = ref(null)

// ── Toast ──────────────────────────────────────────────────────────────────────
const toast = ref({ visible: false, message: '', type: 'success' })
let toastTimer = null

const showToast = (message, type = 'success') => {
  toast.value = { visible: true, message, type }
  clearTimeout(toastTimer)
  toastTimer = setTimeout(() => { toast.value.visible = false }, 3000)
}

// ── Download CV ────────────────────────────────────────────────────────────────
const isDownloading = ref(false)

const downloadCV = async () => {
  if (!process.client || isDownloading.value) return
  isDownloading.value = true
  try {
    const cvAtsUrl = `${window.location.origin}${basePath.value}cv-ats`
    window.open(cvAtsUrl, '_blank')
    await new Promise(r => setTimeout(r, 800))
    showToast('¡CV abierto en nueva pestaña!', 'success')
  } catch {
    showToast('No se pudo abrir el CV', 'error')
  } finally {
    isDownloading.value = false
  }
}

// ── Language bar ───────────────────────────────────────────────────────────────
const langBarRef = ref(null)
const langBarVisible = ref(false)
let langObserver = null

// ── Scroll Reveal ──────────────────────────────────────────────────────────────
let revealObserver = null

// ── i18n helpers ──────────────────────────────────────────────────────────────
const extractStrings = (items) => {
  if (!items || !Array.isArray(items)) return []
  return items.map(item => {
    if (typeof item === 'string') return item
    if (item && typeof item === 'object') {
      if (item.body?.static) return item.body.static
      if (typeof item.static === 'string') return item.static
      if (typeof item.source === 'string') return item.source
    }
    return ''
  }).filter(Boolean)
}

const getTranslationArray = (key) => {
  try {
    const result = tm(key)
    if (Array.isArray(result)) return extractStrings(result)
    const localeMessages = messages.value?.[locale.value]
    if (localeMessages) {
      let value = localeMessages
      for (const k of key.split('.')) {
        value = (value && typeof value === 'object' && k in value) ? value[k] : null
      }
      if (Array.isArray(value)) return extractStrings(value)
    }
    return []
  } catch (e) {
    console.error('Error loading translation:', key, e)
    return []
  }
}

const frontendPoints = computed(() => getTranslationArray('profile.frontend.points'))
const backendPoints  = computed(() => getTranslationArray('profile.backend.points'))
const toolsPoints    = computed(() => getTranslationArray('profile.tools.points'))

// ── Lifecycle ──────────────────────────────────────────────────────────────────
onMounted(async () => {
  if (!process.client) return
  await nextTick()

  // Scroll reveal — doble estrategia para garantizar que funcione en producción:
  // 1. Check inmediato con getBoundingClientRect para los ya visibles en viewport
  // 2. IntersectionObserver para los que entran al hacer scroll
  revealObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('is-visible')
        revealObserver.unobserve(entry.target)
      }
    })
  }, { threshold: 0, rootMargin: '0px' })

  // requestAnimationFrame: esperar a que el browser haya hecho el layout
  // antes de medir posiciones con getBoundingClientRect
  requestAnimationFrame(() => {
    document.querySelectorAll('.reveal').forEach(el => {
      const rect = el.getBoundingClientRect()
      if (rect.top < window.innerHeight && rect.bottom > 0) {
        // Ya está en pantalla — hacer visible de inmediato
        el.classList.add('is-visible')
      } else {
        // Fuera de pantalla — observar para cuando haga scroll
        revealObserver.observe(el)
      }
    })
  })

  // Language bar animada al hacer scroll
  langObserver = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting) {
      langBarVisible.value = true
      langObserver.disconnect()
    }
  }, { threshold: 0.4 })
  if (langBarRef.value) langObserver.observe(langBarRef.value)

  // Precargar la foto de perfil para detectar cuándo está lista
  // background-image no tiene evento onload nativo, usamos Image() API
  const preloadImg = new window.Image()
  preloadImg.onload = () => { profileImageLoaded.value = true }
  preloadImg.onerror = () => { profileImageLoaded.value = true } // mostrar igualmente si falla
  preloadImg.src = profileImagePath.value

  // Modelo 3D: esperar a que el browser esté libre (idle) antes de montar Three.js
  // requestIdleCallback garantiza que el primer paint + la imagen de perfil
  // ya se procesaron antes de inicializar el contexto WebGL
  const mountModel = () => { modelMounted.value = true }

  if ('requestIdleCallback' in window) {
    requestIdleCallback(mountModel, { timeout: 2500 })
  } else {
    setTimeout(mountModel, 600)
  }

  // Card tilt 3D — seguimiento del mouse con perspective
  const setupTilt = () => {
    document.querySelectorAll('.tilt-card').forEach(card => {
      card.addEventListener('mousemove', (e) => {
        const rect = card.getBoundingClientRect()
        const x = (e.clientX - rect.left) / rect.width - 0.5
        const y = (e.clientY - rect.top) / rect.height - 0.5
        card.style.transform = `perspective(700px) rotateX(${y * -6}deg) rotateY(${x * 6}deg) scale3d(1.015,1.015,1.015)`
        card.style.transition = 'transform 0.1s ease'
      })
      card.addEventListener('mouseleave', () => {
        card.style.transform = ''
        card.style.transition = 'transform 0.4s ease'
      })
    })
  }
  setupTilt()

  // ── Parallax orbs — mouse tracking con lerp ──────────────────────────────
  const orbs = Array.from(document.querySelectorAll('.p-orb'))
  const orbStates = orbs.map(orb => ({
    el: orb,
    speed: parseFloat(orb.dataset.speed || 0.05),
    cx: 0, cy: 0,   // current (lerped)
    tx: 0, ty: 0,   // target
  }))

  let mouseX = 0, mouseY = 0
  const onMouseMove = (e) => {
    mouseX = (e.clientX / window.innerWidth  - 0.5) * 2
    mouseY = (e.clientY / window.innerHeight - 0.5) * 2
    orbStates.forEach(o => {
      const dist = Math.min(window.innerWidth, window.innerHeight) * 0.5
      o.tx = mouseX * dist * o.speed
      o.ty = mouseY * dist * o.speed
    })
  }
  window.addEventListener('mousemove', onMouseMove, { passive: true })

  let rafId
  const lerp = (a, b, t) => a + (b - a) * t
  const animateOrbs = () => {
    orbStates.forEach(o => {
      o.cx = lerp(o.cx, o.tx, 0.06)
      o.cy = lerp(o.cy, o.ty, 0.06)
      o.el.style.transform = `translate(calc(-50% + ${o.cx}px), calc(-50% + ${o.cy}px))`
    })
    rafId = requestAnimationFrame(animateOrbs)
  }
  animateOrbs()

  // Guardar cleanup en variables de módulo
  window.__parallaxCleanup = () => {
    window.removeEventListener('mousemove', onMouseMove)
    cancelAnimationFrame(rafId)
  }
})

onUnmounted(() => {
  revealObserver?.disconnect()
  langObserver?.disconnect()
  clearTimeout(toastTimer)
  window.__parallaxCleanup?.()
  delete window.__parallaxCleanup
})
</script>

<style>
.material-symbols-outlined {
  font-variation-settings: 'FILL' 0, 'wght' 400, 'GRAD' 0, 'opsz' 24;
}

/* ══════════════════════════════════════════════
   PARALLAX SCENE — orbs que siguen el mouse
══════════════════════════════════════════════ */
.parallax-scene {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 0;
  overflow: hidden;
}

.p-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  will-change: transform;
  opacity: 0;
  animation: orb-appear 1.2s ease forwards;
}

/* Posiciones base (left/top en %) */
.p-orb-1 {
  width: 55vw; height: 55vw;
  left: 5%; top: -10%;
  background: radial-gradient(circle, rgba(59,130,246,0.22) 0%, transparent 70%);
  animation-delay: 0s;
}
.p-orb-2 {
  width: 45vw; height: 45vw;
  left: 65%; top: 10%;
  background: radial-gradient(circle, rgba(139,92,246,0.18) 0%, transparent 70%);
  animation-delay: 0.2s;
}
.p-orb-3 {
  width: 40vw; height: 40vw;
  left: 30%; top: 55%;
  background: radial-gradient(circle, rgba(6,182,212,0.15) 0%, transparent 70%);
  animation-delay: 0.4s;
}
.p-orb-4 {
  width: 35vw; height: 35vw;
  left: 80%; top: 60%;
  background: radial-gradient(circle, rgba(16,185,129,0.12) 0%, transparent 70%);
  animation-delay: 0.6s;
}
.p-orb-5 {
  width: 30vw; height: 30vw;
  left: -5%; top: 70%;
  background: radial-gradient(circle, rgba(245,158,11,0.10) 0%, transparent 70%);
  animation-delay: 0.8s;
}

.dark .p-orb-1 { background: radial-gradient(circle, rgba(59,130,246,0.30) 0%, transparent 70%); }
.dark .p-orb-2 { background: radial-gradient(circle, rgba(139,92,246,0.25) 0%, transparent 70%); }
.dark .p-orb-3 { background: radial-gradient(circle, rgba(6,182,212,0.22) 0%, transparent 70%); }
.dark .p-orb-4 { background: radial-gradient(circle, rgba(16,185,129,0.18) 0%, transparent 70%); }
.dark .p-orb-5 { background: radial-gradient(circle, rgba(245,158,11,0.14) 0%, transparent 70%); }

@keyframes orb-appear {
  from { opacity: 0; transform: translate(-50%, -50%) scale(0.8); }
  to   { opacity: 1; transform: translate(-50%, -50%) scale(1); }
}


/* ══════════════════════════════════════════════
   AVATAR — ANILLO GIRATORIO
══════════════════════════════════════════════ */
.avatar-ring-outer {
  position: absolute;
  inset: -3px;
  border-radius: 50%;
  padding: 3px;
  background: conic-gradient(#3b82f6, #8b5cf6, #06b6d4, #3b82f6);
  animation: ring-spin 3s linear infinite;
  z-index: 0;
}
.avatar-ring-inner {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: var(--background-light);
}
.dark .avatar-ring-inner { background: var(--background-dark); }
@keyframes ring-spin { to { transform: rotate(360deg); } }

.hero-avatar {
  position: relative;
  display: inline-flex;
  animation: hero-up 0.45s ease both;
}
.hero-info {
  animation: hero-up 0.45s ease 0.07s both;
}
@keyframes hero-up {
  from { opacity: 0; transform: translateY(18px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* ══════════════════════════════════════════════
   TYPEWRITER
══════════════════════════════════════════════ */
.typewriter-wrap { overflow: hidden; }
.typewriter {
  display: inline-block;
  overflow: hidden;
  white-space: nowrap;
  border-right: 2px solid currentColor;
  animation:
    typing 1.8s steps(30, end) 0.3s both,
    blink  0.75s step-end infinite;
  max-width: 100%;
}
@keyframes typing { from { width: 0; } to { width: 100%; } }
@keyframes blink  { 50% { border-color: transparent; } }

/* ══════════════════════════════════════════════
   BOTÓN CON GLOW
══════════════════════════════════════════════ */
.btn-glow {
  background: linear-gradient(135deg, #3b82f6, #6366f1);
  box-shadow: 0 0 0 0 rgba(99,102,241,0.4);
  transition: box-shadow 0.3s ease, transform 0.15s ease;
}
.btn-glow:hover:not(:disabled) {
  box-shadow: 0 0 20px 6px rgba(99,102,241,0.35), 0 4px 16px rgba(59,130,246,0.3);
  transform: translateY(-1px);
}

/* ══════════════════════════════════════════════
   GLASSMORPHISM CARDS
══════════════════════════════════════════════ */
.glass-card {
  border: 1px solid rgba(255,255,255,0.6);
  box-shadow: 0 4px 24px rgba(0,0,0,0.06);
  transition: box-shadow 0.3s ease, border-color 0.3s ease;
}
.glass-card:hover {
  box-shadow: 0 8px 32px rgba(59,130,246,0.12), 0 2px 8px rgba(0,0,0,0.06);
  border-color: rgba(59,130,246,0.3);
}
.dark .glass-card {
  border-color: rgba(255,255,255,0.07);
  box-shadow: 0 4px 24px rgba(0,0,0,0.3);
}
.dark .glass-card:hover {
  box-shadow: 0 8px 32px rgba(59,130,246,0.2), 0 2px 8px rgba(0,0,0,0.4);
  border-color: rgba(59,130,246,0.4);
}

/* ══════════════════════════════════════════════
   SKELETON SHIMMER
══════════════════════════════════════════════ */
.skeleton-shimmer {
  background: linear-gradient(90deg, var(--border-light) 25%, #e0e7ef 50%, var(--border-light) 75%);
  background-size: 200% 100%;
  animation: shimmer 1.6s infinite;
}
.dark .skeleton-shimmer {
  background: linear-gradient(90deg, var(--border-dark) 25%, #2d3748 50%, var(--border-dark) 75%);
  background-size: 200% 100%;
}
@keyframes shimmer {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

/* ══════════════════════════════════════════════
   FADE DEL CANVAS 3D
══════════════════════════════════════════════ */
.model-fade-enter-active { transition: opacity 0.6s ease; }
.model-fade-enter-from   { opacity: 0; }

/* ══════════════════════════════════════════════
   SCROLL REVEAL — escala + fade (más impacto)
══════════════════════════════════════════════ */
.reveal {
  opacity: 0;
  transform: translateY(32px) scale(0.98);
  transition: opacity 0.6s cubic-bezier(0.16,1,0.3,1), transform 0.6s cubic-bezier(0.16,1,0.3,1);
  will-change: opacity, transform;
}
.reveal.is-visible {
  opacity: 1;
  transform: translateY(0) scale(1);
  will-change: auto;
}

/* ══════════════════════════════════════════════
   TOAST
══════════════════════════════════════════════ */
.toast-enter-active, .toast-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.toast-enter-from, .toast-leave-to {
  opacity: 0;
  transform: translateY(-12px) scale(0.95);
}

/* ══════════════════════════════════════════════
   BUTTON SPINNER
══════════════════════════════════════════════ */
.btn-spinner {
  display: inline-block;
  width: 16px;
  height: 16px;
  border: 2px solid rgba(255,255,255,0.4);
  border-top-color: #fff;
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
  flex-shrink: 0;
}
@keyframes spin { to { transform: rotate(360deg); } }

/* ══════════════════════════════════════════════
   z-index — contenido sobre el parallax scene
══════════════════════════════════════════════ */
.layout-container { position: relative; z-index: 1; }
</style>
