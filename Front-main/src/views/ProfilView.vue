<template>
  <div class="main-container">
    <!-- Profil Section -->
    <div class="container profile-section">
      <h2 class="heading">Mon Profil</h2>
      <div class="profile-photo-section">
        <img :src="require('@/assets/user.jpg')" alt="Photo de profil" class="profile-photo" />
      </div>
      <form @submit.prevent="saveChanges" class="form">
        <div v-if="!isEditing">
          <p>Prénom : {{ profile.name }}</p>
          <p>Nom : {{ profile.surname }}</p>
          <p>Email : {{ profile.email }}</p>
          <p>Mot de passe : ********</p>
          <button type="button" @click="isEditing = true" class="login-button">Modifier</button>
        </div>
        <div v-if="isEditing">
          <input type="text" v-model="profile.name" placeholder="Changer le prénom" class="input" />
          <input type="text" v-model="profile.surname" placeholder="Changer le nom" class="input" />
          <input type="email" v-model="profile.email" placeholder="Changer l'email" class="input" />
          <input type="password" v-model="profile.password" placeholder="Changer le mot de passe" class="input" />
          <button type="submit" class="login-button">Enregistrer les changements</button>
          <button type="button" @click="isEditing = false" class="login-button cancel-button">Annuler</button>
        </div>
      </form>
    </div>

    <!-- Statistiques Section -->
    <div class="container2 stats-section">
      <h2 class="heading">Statistiques des tâches</h2>
      <div class="charts-section">
        <PieChart :statistics="taskStatistics" />
      </div>
    </div>
 </div>
    <!-- Line Chart Section -->
    <div class="container3 linechart-section">

      <LineChart :chartData="weeklyData || []" />

    </div>

    <!-- Category Bar Chart -->
    <div class="container4 category-bar-chart">
      <h2 class="heading">Nombre de tâches par catégorie</h2>
      <CategoryBarChart :chartData="categoryData"  />
    </div>
 <!-- Cards Section -->
 <div class="cards-container">
  <!-- Carte 1: Total des tâches -->
  <StatCard 
    title="Réalisation moyenne" 
    style="background-color:#240046 ;" 
    endpoint="http://localhost:3000/api/tasks/average-completion-time" 
  />
  <!-- Carte 2: Achèvement du jour -->
  <StatCard 
    title="Achèvement du jour" 
    style="background-color:#3c096c ;" 
    endpoint="http://localhost:3000/api/tasks/completed-percentage" 
  />
  <!-- Carte 3: Tâches à venir -->
  <StatCard 
    title="Urgence du jour" 
    style="background-color:#5a189a ;" 
    endpoint="http://localhost:3000/api/tasks/urgent-ratio" 
  />

  <!-- Carte 4: Urgence du jour -->
 <StatCard 
    title="Tâches à venir" 
    style="background-color:#7b2cbf ;" 
    endpoint="http://localhost:3000/api/tasks/upcoming-total" 
  />
</div>
 <div class="priorite">
  <TaskPriority />
 </div>
</template>

<script>
import axios from "axios";
import PieChart from "@/components/charts/PieChart.vue";
import LineChart from "@/components/charts/LineChart.vue"; // Import the LineChart
import CategoryBarChart from "@/components/charts/CategoryBarChart.vue"; // Import the CategoryBarChart
import StatCard from "@/components/charts/StatCard.vue";
import TaskPriority from "@/components/charts/TaskPriority.vue";
export default {
  components: {
    PieChart,
    LineChart,
    CategoryBarChart, 
    StatCard,
    TaskPriority,

  },
  data() {
    return {
      isEditing: false,
      profile: {
        name: "",
        surname: "",
        email: "",
        password: "",
      },
      taskStatistics: [], // For holding statistics data
      weeklyData: [], // For holding weekly task data
      categoryData: [], // For holding category statistics data
   
       
    };
  },
  methods: {
    async fetchProfileData() {
      try {
        const response = await axios.get("http://localhost:3000/profile", { withCredentials: true });
        const data = response.data || {};
        this.profile.name = data.name || "Nom non défini";
        this.profile.surname = data.surname || "Prénom non défini";
        this.profile.email = data.email || "Email non défini";
      } catch (error) {
        console.error("Erreur lors du chargement des données du profil :", error);
      }
    },
    async fetchStatistics() {
      try {
        const response = await axios.get("http://localhost:3000/api/statistiques", { withCredentials: true });
        this.taskStatistics = response.data || [];
      } catch (error) {
        console.error("Erreur lors du chargement des statistiques :", error);
      }
    },
    async fetchWeeklyData() {
  try {
    const response = await axios.get("http://localhost:3000/api/statistiques/weekly-completed");
    this.weeklyData = response.data; // Récupère les données de la semaine
  } catch (error) {
    console.error("Erreur lors de la récupération des données:", error);
  }
},
    async fetchCategoryData() {
      try {
        const response = await axios.get("http://localhost:3000/api/statistiques/categories");
        this.categoryData = response.data || [];
      } catch (error) {
        console.error("Erreur lors de la récupération des données de catégories:", error);
      }
    },
    
    async saveChanges() {
      const updatedProfile = {
        name: this.profile.name,
        surname: this.profile.surname,
        email: this.profile.email,
        password: this.profile.password,
      };
      try {
        await axios.put("http://localhost:3000/profile", updatedProfile, { withCredentials: true });
        alert("Les changements ont été enregistrés !");
      } catch (error) {
        console.error("Erreur lors de l'enregistrement :", error);
      }
    },
  },
  mounted() {
    this.fetchProfileData();
    this.fetchStatistics();
    this.fetchWeeklyData();
    this.fetchCategoryData(); 
    
  },
};
</script>


<style scoped>


.dark-mode .container,
.dark-mode .container2 {
  background-color:rgb(55, 54, 56);
  color:rgba(209, 209, 209, 0.858) ;
  box-shadow: 5px 18px 22px rgba(0, 0, 0, 0.324);
  border:none;
}

.dark-mode .input {
  background:rgb(108, 108, 108);
  color:  rgba(209, 209, 209, 0.858) ;
  box-shadow: 5px 18px 22px rgba(0, 0, 0, 0.324);
}


.dark-mode .circle {
  background: conic-gradient(
    #bb86fc calc(  rgba(209, 209, 209, 0.858) * 1%),
    #444 0%
  );
}
.dark-mode .heading{
  color:hsl(268, 75%, 67%);
}
.form {
 font-size: large;
}
.main-container {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center; /* Center elements on smaller screens */
}

.container {
  background: #f8f9fd;
  border-radius: 20px;
  padding: 30px;
  border: 5px solid #fff;
  box-shadow: rgba(182, 133, 215, 0.5) 0px 15px 20px -10px;
  margin: 20px; /* Adjust for better spacing on smaller screens */
  flex: 1; /* Make the container grow and shrink */
  max-width: 500px;
}

.container2 {
  background: #f8f9fd;
  border-radius: 20px;
  padding: 30px;
  border: 5px solid #fff;
  box-shadow: rgba(182, 133, 215, 0.5) 0px 15px 20px -10px;
  margin: 20px; /* Adjust for better spacing on smaller screens */
  flex: 2; /* Allow more space for stats */
  max-width: 500px;
  
}
.container4{
  background: #f8f9fd;
  border-radius: 20px;
  padding: 30px;
  border: 5px solid #fff;
  box-shadow: rgba(182, 133, 215, 0.5) 0px 15px 20px -10px;
  margin-left: 330px;
  margin-bottom: 100px;
  width: 60%;


}
.statistics {
  display: flex;
  flex-wrap: wrap; /* Allow cards to stack on smaller screens */
  justify-content: space-around; /* Distribute cards evenly */
  gap: 50px;
}

.stat-card {
  flex: 1 1 calc(33.33% - 40px); /* Ensure cards take up equal space */
  text-align: center;
  
}

/* Media Query for Small Screens */
@media (max-width: 768px) {
  .main-container {
    flex-direction: column; /* Stack elements vertically */
  }

  .container {
    margin-left:100px; /* Remove margin for smaller screens */
    max-width: 100%;

  }

  .container2 {
    width: 80%; /* Take full width */
    margin-left:100px ;
  }
  .container3 {
    max-width: 80%; /* Take full width */
  
  }

  .stat-card {
    flex: 1 1 100%; /* Full width for each stat card */
  }
}
.profile-section {
  max-width: 400px;
}
.heading {
  text-align: center;
  font-weight: 900;
  font-size: 28px;
  color: rgb(82, 26, 122);
  margin-bottom: 20px;
}

.profile-photo-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 50px;
}

.profile-photo {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  object-fit: cover;
  margin-bottom: 30px;
}

.input {
  width: 100%;
  background: white;
  border: none;
  padding: 10px 15px;
  border-radius: 15px;
  margin-top: 10px;
  box-shadow: rgba(238, 207, 255, 0.8) 0px 10px 10px -5px;
}

.input:focus {
  outline: none;
  border-inline: 2px solid #7b12d1;
}

.login-button, .cancel-button {
  width: 100%;
  background: linear-gradient(45deg, rgb(162, 16, 211), rgb(114, 18, 209));
  color: white;
  padding: 12px;
  border-radius: 15px;
  margin-top: 15px;
  font-weight: bold;
  text-align: center;
  transition: 0.3s ease;
}

.cancel-button {
  background: #ccc;
  color: black;
}

.charts-section > * {
  flex: 1 1 45%;
 padding-right:80px ;
}
.container3 {
  background: #f8f9fd;
  border-radius: 20px;
  padding: 30px;
  border: 5px solid #fff;
  box-shadow: rgba(182, 133, 215, 0.5) 0px 15px 20px -10px;
   width: 60%;


}

/* Ensuring the chart section does not break layout */
.linechart-section {
  margin-top: 30px;
  margin-left: 330px;
  margin-bottom: 50px;
}
.linechart-section {
  flex: 1;

}
.linechart-section h2 {
  text-align: center;
  font-size: 24px;
  color: rgb(82, 26, 122);
  margin-bottom: 20px;
}
.category-bar-chart h2 {
  text-align: center;
  font-size: 24px;
  color: rgb(82, 26, 122);
  margin-bottom: 20px;
}/* 
.card-section {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap; 
  margin: 50px auto;
  gap: 0px; 
}

.card {
  color: white;
  padding: 60px;
  border-radius: 10px;
  text-align: center;
  font-size: 18px;
  font-weight: bold;
  width: 200px;

  box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.2);
  transition: transform 0.3s ease;
}

.card:hover {
  transform: scale(1.05); 
}
*/
.cards-container {
  display: flex;
  flex-wrap: wrap;
  gap: 30px;
  width: 80%;
  margin-left:300px;
  margin-bottom: 400px;
} 
.priorite{
  width:60%;
  margin:400px ;
}
</style>