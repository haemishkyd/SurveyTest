<script setup lang="ts">
import 'survey-core/survey-core.css';
import { Model } from 'survey-core';
import { SurveyComponent } from 'survey-vue3-ui';

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

const alertResults = (sender: any) => {
  const results = JSON.stringify(sender.data);
  alert(results);
}

const survey = new Model(surveyJson);
survey.onComplete.add(alertResults);
</script>

<template>
  <SurveyComponent :model="survey" />
</template>