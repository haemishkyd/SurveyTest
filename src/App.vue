<script setup lang="ts">
import 'survey-core/survey-core.css';
import { Model } from 'survey-core';
import { SurveyComponent } from 'survey-vue3-ui';
import axios from 'axios';
import { ref } from 'vue';

const surveyJson = {
  pages: [{
    elements: [{
      type: "text",
      name: "FirstName",
      title: "Enter your first name:"
    }, {
      type: "text",
      name: "LastName",
      title: "Enter your last name:"
    }, {
      name: "purchased-item",
      title: "Have you ever purchased our product?",
      type: "radiogroup",
      choices: [        
        { value: 2, text: "No" },
        { value: 1, text: "Yes" }
      ],
    }]
  }, {
    elements: [{
      name: "satisfaction-score",
      title: "{FirstName} how would you describe your experience with our product?",
      type: "radiogroup",
      choices: [
        { value: 5, text: "Fully satisfying" },
        { value: 4, text: "Generally satisfying" },
        { value: 3, text: "Neutral" },
        { value: 2, text: "Rather unsatisfying" },
        { value: 1, text: "Not satisfying at all" }
      ],
      isRequired: true,
      visibleIf: "{purchased-item} == 1"
    }]
  }, {
    elements: [{
      name: "what-would-make-you-more-satisfied",
      title: "{FirstName} what can we do to make your experience more satisfying?",
      type: "comment",
    }, {
      name: "nps-score",
      title: "On a scale of zero to ten, how likely are you to recommend our product to a friend or colleague?",
      type: "rating",
      rateMin: 0,
      rateMax: 10
    }],
  }, {
    elements: [{
      name: "how-can-we-improve",
      title: "{FirstName}, in your opinion, how could we improve our product?",
      type: "comment"
    }],
  }, {
    elements: [{
      name: "disappointing-experience",
      title: "{FirstName}, please let us know why you had such a disappointing experience with our product.",
      type: "comment"
    }],
  }]
};

// Loading and response state tracking
const isLoading = ref(false);
const apiResponse = ref(null);
const apiError = ref(null);

// Function to submit survey results to an API
const submitSurveyToAPI = async (data) => {
  isLoading.value = true;
  apiError.value = null;
  
  try {
    // Replace with your actual API endpoint
    const response = await axios.post('http://192.168.1.61:5000/api/add_surveydata', data);
    apiResponse.value = response.data;
    console.log('API response:', response.data);
    return response.data;
  } catch (error) {
    apiError.value = error.message || 'An error occurred while submitting the survey';
    console.error('API error:', error);
    throw error;
  } finally {
    isLoading.value = false;
  }
};

const alertResults = async (sender: any) => {
  const results = JSON.stringify(sender.data);
  
  try {
    // Send data to API
    const apiResponseData = await submitSurveyToAPI(sender.data);
    alert('Survey submitted successfully! Thank you for your feedback.');
  } catch (error) {
    // Show error alert if API call fails
    alert(`Error submitting survey: ${apiError.value}. Your results: ${results}`);
  }
}

const survey = new Model(surveyJson);
survey.onComplete.add(alertResults);
</script>

<template>
  <div>
    <SurveyComponent :model="survey" />
    <div v-if="isLoading" class="loading-indicator">
      Submitting your response...
    </div>
    <div v-if="apiError" class="error-message">
      {{ apiError }}
    </div>
  </div>
</template>

<style scoped>
.loading-indicator {
  margin-top: 1rem;
  color: #2c3e50;
  font-weight: bold;
}
.error-message {
  margin-top: 1rem;
  color: #ff0000;
  font-weight: bold;
}
</style>