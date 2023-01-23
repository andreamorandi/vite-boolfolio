<script>
import axios from 'axios';
import AppLoader from '../components/AppLoader.vue';
import ProjectCard from '../components/ProjectCard.vue';
import { store } from '../store';

export default {
    name: "ProjectsList",
    components: { ProjectCard, AppLoader },
    data() {
        return {
            store,
            projects: [],
            types: [],
            selectedType: "",
            currentPage: 1,
            lastPage: null,
            totalProjects: null,
            loading: false
        };
    },
    created() {
        this.getProjects(1);
        this.getTypes();
    },
    methods: {
        getProjects(page) {
            const options = {
                params: {
                    page,
                    ...this.selectedType && { type_id: this.selectedType }
                }
            }
            this.loading = true;
            axios.get(`${this.store.apiBaseUrl}/api/projects`, options).then(resp => {
                this.projects = resp.data.results.data;
                this.currentPage = resp.data.results.current_page;
                this.lastPage = resp.data.results.last_page;
                this.totalProjects = resp.data.results.total;
                this.loading = false;
            });
        },
        getTypes() {
            axios.get(`${this.store.apiBaseUrl}/api/types`).then(resp => {
                this.types = resp.data.results;
            });
        }
    },

}
</script>

<template>
    <div class="container">
        <h2 class="text-center mb-3">Tutti i nostri progetti</h2>
        <AppLoader v-if="loading" />
        <div v-else class="row justify-content-center py-4">
            <div class="col-11 col-md-10 col-lg-8">
                <div class="d-flex justify-content-between mb-4">
                    <form @submit.prevent="getProjects(1)" action="" class="d-flex">
                        <select id="" class="form-select" v-model="selectedType">
                            <option value="">Tutti</option>
                            <option v-for="type in types" :value="type.id">{{ type.name }}</option>
                        </select>
                        <button type="submit" class="btn btn-success">Filtra</button>
                    </form>
                    <p class="text-end">Totale {{ totalProjects }} progetti trovati</p>
                </div>
                <ProjectCard :project="project" v-for="project in projects" :key="project.id" />
                <nav class="navigation d-flex justify-content-between">
                    <div>
                        Pagina {{ currentPage }} di {{ lastPage }}
                    </div>
                    <div>
                        <a class="btn btn-success me-2" :class="currentPage === 1 ? 'disabled' : ''" href=""
                            @click.prevent="getProjects(currentPage - 1)">Indietro</a>
                        <a class="btn btn-success" :class="{ 'disabled': currentPage === lastPage }" href=""
                            @click.prevent="getProjects(currentPage + 1)">Avanti</a>
                    </div>
                </nav>
            </div>
        </div>
    </div>
</template>

<style lang="scss">

</style>