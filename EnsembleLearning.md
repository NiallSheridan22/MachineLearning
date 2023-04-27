{
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/NiallSheridan22/MachineLearning/blob/main/EnsembleLearning.md\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "The goal of this study is to identify the most significant factors for recognizing credit card fraud trends using Feature Selection, and then to assess the effectiveness of several Machine Learning models (using these variables) functioning both independently and together using Ensemble Learning.\n"
      ],
      "metadata": {
        "id": "2_GXV0eNOSnp"
      }
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "7ngEsnne-AI5"
      },
      "source": [
        "Ensemble learning the process by which multiple models, such as classifiers or experts, are strategically generated and combined to solve a particular computational intelligence problem."
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "wqUQU-DN-Y2X"
      },
      "outputs": [],
      "source": [
        "#Import Libraries that are needed.\n",
        "# Base Libraries\n",
        "import matplotlib.pyplot as plt  \n",
        "import pandas as pd\n",
        "import numpy as np\n",
        "from numpy import set_printoptions\n",
        "from numpy import mean\n",
        "# Models\n",
        "from sklearn.model_selection import train_test_split\n",
        "from sklearn.model_selection import GridSearchCV\n",
        "from sklearn.neighbors import KNeighborsClassifier\n",
        "from sklearn.linear_model import LogisticRegression\n",
        "from sklearn.ensemble import RandomForestClassifier\n",
        "from sklearn.ensemble import ExtraTreesClassifier\n",
        "from sklearn.ensemble import VotingClassifier\n",
        "from sklearn.ensemble import StackingClassifier\n",
        "# Metrics\n",
        "from sklearn.metrics import accuracy_score\n",
        "from sklearn.metrics import auc\n",
        "from sklearn.metrics import roc_curve\n",
        "from sklearn.metrics import plot_confusion_matrix\n",
        "\n"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "DfzdoBPwUnLE",
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 473
        },
        "outputId": "232cd397-c7c6-4b2b-9542-403ddc49fdf8"
      },
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "69415\n"
          ]
        },
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "   Time        V1        V2        V3        V4        V5        V6        V7  \\\n",
              "0     0 -1.359807 -0.072781  2.536347  1.378155 -0.338321  0.462388  0.239599   \n",
              "1     0  1.191857  0.266151  0.166480  0.448154  0.060018 -0.082361 -0.078803   \n",
              "2     1 -1.358354 -1.340163  1.773209  0.379780 -0.503198  1.800499  0.791461   \n",
              "3     1 -0.966272 -0.185226  1.792993 -0.863291 -0.010309  1.247203  0.237609   \n",
              "4     2 -1.158233  0.877737  1.548718  0.403034 -0.407193  0.095921  0.592941   \n",
              "5     2 -0.425966  0.960523  1.141109 -0.168252  0.420987 -0.029728  0.476201   \n",
              "6     4  1.229658  0.141004  0.045371  1.202613  0.191881  0.272708 -0.005159   \n",
              "7     7 -0.644269  1.417964  1.074380 -0.492199  0.948934  0.428118  1.120631   \n",
              "8     7 -0.894286  0.286157 -0.113192 -0.271526  2.669599  3.721818  0.370145   \n",
              "9     9 -0.338262  1.119593  1.044367 -0.222187  0.499361 -0.246761  0.651583   \n",
              "\n",
              "         V8        V9  ...       V21       V22       V23       V24       V25  \\\n",
              "0  0.098698  0.363787  ... -0.018307  0.277838 -0.110474  0.066928  0.128539   \n",
              "1  0.085102 -0.255425  ... -0.225775 -0.638672  0.101288 -0.339846  0.167170   \n",
              "2  0.247676 -1.514654  ...  0.247998  0.771679  0.909412 -0.689281 -0.327642   \n",
              "3  0.377436 -1.387024  ... -0.108300  0.005274 -0.190321 -1.175575  0.647376   \n",
              "4 -0.270533  0.817739  ... -0.009431  0.798278 -0.137458  0.141267 -0.206010   \n",
              "5  0.260314 -0.568671  ... -0.208254 -0.559825 -0.026398 -0.371427 -0.232794   \n",
              "6  0.081213  0.464960  ... -0.167716 -0.270710 -0.154104 -0.780055  0.750137   \n",
              "7 -3.807864  0.615375  ...  1.943465 -1.015455  0.057504 -0.649709 -0.415267   \n",
              "8  0.851084 -0.392048  ... -0.073425 -0.268092 -0.204233  1.011592  0.373205   \n",
              "9  0.069539 -0.736727  ... -0.246914 -0.633753 -0.120794 -0.385050 -0.069733   \n",
              "\n",
              "        V26       V27       V28  Amount  Class  \n",
              "0 -0.189115  0.133558 -0.021053  149.62    0.0  \n",
              "1  0.125895 -0.008983  0.014724    2.69    0.0  \n",
              "2 -0.139097 -0.055353 -0.059752  378.66    0.0  \n",
              "3 -0.221929  0.062723  0.061458  123.50    0.0  \n",
              "4  0.502292  0.219422  0.215153   69.99    0.0  \n",
              "5  0.105915  0.253844  0.081080    3.67    0.0  \n",
              "6 -0.257237  0.034507  0.005168    4.99    0.0  \n",
              "7 -0.051634 -1.206921 -1.085339   40.80    0.0  \n",
              "8 -0.384157  0.011747  0.142404   93.20    0.0  \n",
              "9  0.094199  0.246219  0.083076    3.68    0.0  \n",
              "\n",
              "[10 rows x 31 columns]"
            ],
            "text/html": [
              "\n",
              "  <div id=\"df-7da4466b-3fa8-481f-865f-750b4f4d98bf\">\n",
              "    <div class=\"colab-df-container\">\n",
              "      <div>\n",
              "<style scoped>\n",
              "    .dataframe tbody tr th:only-of-type {\n",
              "        vertical-align: middle;\n",
              "    }\n",
              "\n",
              "    .dataframe tbody tr th {\n",
              "        vertical-align: top;\n",
              "    }\n",
              "\n",
              "    .dataframe thead th {\n",
              "        text-align: right;\n",
              "    }\n",
              "</style>\n",
              "<table border=\"1\" class=\"dataframe\">\n",
              "  <thead>\n",
              "    <tr style=\"text-align: right;\">\n",
              "      <th></th>\n",
              "      <th>Time</th>\n",
              "      <th>V1</th>\n",
              "      <th>V2</th>\n",
              "      <th>V3</th>\n",
              "      <th>V4</th>\n",
              "      <th>V5</th>\n",
              "      <th>V6</th>\n",
              "      <th>V7</th>\n",
              "      <th>V8</th>\n",
              "      <th>V9</th>\n",
              "      <th>...</th>\n",
              "      <th>V21</th>\n",
              "      <th>V22</th>\n",
              "      <th>V23</th>\n",
              "      <th>V24</th>\n",
              "      <th>V25</th>\n",
              "      <th>V26</th>\n",
              "      <th>V27</th>\n",
              "      <th>V28</th>\n",
              "      <th>Amount</th>\n",
              "      <th>Class</th>\n",
              "    </tr>\n",
              "  </thead>\n",
              "  <tbody>\n",
              "    <tr>\n",
              "      <th>0</th>\n",
              "      <td>0</td>\n",
              "      <td>-1.359807</td>\n",
              "      <td>-0.072781</td>\n",
              "      <td>2.536347</td>\n",
              "      <td>1.378155</td>\n",
              "      <td>-0.338321</td>\n",
              "      <td>0.462388</td>\n",
              "      <td>0.239599</td>\n",
              "      <td>0.098698</td>\n",
              "      <td>0.363787</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.018307</td>\n",
              "      <td>0.277838</td>\n",
              "      <td>-0.110474</td>\n",
              "      <td>0.066928</td>\n",
              "      <td>0.128539</td>\n",
              "      <td>-0.189115</td>\n",
              "      <td>0.133558</td>\n",
              "      <td>-0.021053</td>\n",
              "      <td>149.62</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>1</th>\n",
              "      <td>0</td>\n",
              "      <td>1.191857</td>\n",
              "      <td>0.266151</td>\n",
              "      <td>0.166480</td>\n",
              "      <td>0.448154</td>\n",
              "      <td>0.060018</td>\n",
              "      <td>-0.082361</td>\n",
              "      <td>-0.078803</td>\n",
              "      <td>0.085102</td>\n",
              "      <td>-0.255425</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.225775</td>\n",
              "      <td>-0.638672</td>\n",
              "      <td>0.101288</td>\n",
              "      <td>-0.339846</td>\n",
              "      <td>0.167170</td>\n",
              "      <td>0.125895</td>\n",
              "      <td>-0.008983</td>\n",
              "      <td>0.014724</td>\n",
              "      <td>2.69</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>2</th>\n",
              "      <td>1</td>\n",
              "      <td>-1.358354</td>\n",
              "      <td>-1.340163</td>\n",
              "      <td>1.773209</td>\n",
              "      <td>0.379780</td>\n",
              "      <td>-0.503198</td>\n",
              "      <td>1.800499</td>\n",
              "      <td>0.791461</td>\n",
              "      <td>0.247676</td>\n",
              "      <td>-1.514654</td>\n",
              "      <td>...</td>\n",
              "      <td>0.247998</td>\n",
              "      <td>0.771679</td>\n",
              "      <td>0.909412</td>\n",
              "      <td>-0.689281</td>\n",
              "      <td>-0.327642</td>\n",
              "      <td>-0.139097</td>\n",
              "      <td>-0.055353</td>\n",
              "      <td>-0.059752</td>\n",
              "      <td>378.66</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>3</th>\n",
              "      <td>1</td>\n",
              "      <td>-0.966272</td>\n",
              "      <td>-0.185226</td>\n",
              "      <td>1.792993</td>\n",
              "      <td>-0.863291</td>\n",
              "      <td>-0.010309</td>\n",
              "      <td>1.247203</td>\n",
              "      <td>0.237609</td>\n",
              "      <td>0.377436</td>\n",
              "      <td>-1.387024</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.108300</td>\n",
              "      <td>0.005274</td>\n",
              "      <td>-0.190321</td>\n",
              "      <td>-1.175575</td>\n",
              "      <td>0.647376</td>\n",
              "      <td>-0.221929</td>\n",
              "      <td>0.062723</td>\n",
              "      <td>0.061458</td>\n",
              "      <td>123.50</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>4</th>\n",
              "      <td>2</td>\n",
              "      <td>-1.158233</td>\n",
              "      <td>0.877737</td>\n",
              "      <td>1.548718</td>\n",
              "      <td>0.403034</td>\n",
              "      <td>-0.407193</td>\n",
              "      <td>0.095921</td>\n",
              "      <td>0.592941</td>\n",
              "      <td>-0.270533</td>\n",
              "      <td>0.817739</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.009431</td>\n",
              "      <td>0.798278</td>\n",
              "      <td>-0.137458</td>\n",
              "      <td>0.141267</td>\n",
              "      <td>-0.206010</td>\n",
              "      <td>0.502292</td>\n",
              "      <td>0.219422</td>\n",
              "      <td>0.215153</td>\n",
              "      <td>69.99</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>5</th>\n",
              "      <td>2</td>\n",
              "      <td>-0.425966</td>\n",
              "      <td>0.960523</td>\n",
              "      <td>1.141109</td>\n",
              "      <td>-0.168252</td>\n",
              "      <td>0.420987</td>\n",
              "      <td>-0.029728</td>\n",
              "      <td>0.476201</td>\n",
              "      <td>0.260314</td>\n",
              "      <td>-0.568671</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.208254</td>\n",
              "      <td>-0.559825</td>\n",
              "      <td>-0.026398</td>\n",
              "      <td>-0.371427</td>\n",
              "      <td>-0.232794</td>\n",
              "      <td>0.105915</td>\n",
              "      <td>0.253844</td>\n",
              "      <td>0.081080</td>\n",
              "      <td>3.67</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>6</th>\n",
              "      <td>4</td>\n",
              "      <td>1.229658</td>\n",
              "      <td>0.141004</td>\n",
              "      <td>0.045371</td>\n",
              "      <td>1.202613</td>\n",
              "      <td>0.191881</td>\n",
              "      <td>0.272708</td>\n",
              "      <td>-0.005159</td>\n",
              "      <td>0.081213</td>\n",
              "      <td>0.464960</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.167716</td>\n",
              "      <td>-0.270710</td>\n",
              "      <td>-0.154104</td>\n",
              "      <td>-0.780055</td>\n",
              "      <td>0.750137</td>\n",
              "      <td>-0.257237</td>\n",
              "      <td>0.034507</td>\n",
              "      <td>0.005168</td>\n",
              "      <td>4.99</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>7</th>\n",
              "      <td>7</td>\n",
              "      <td>-0.644269</td>\n",
              "      <td>1.417964</td>\n",
              "      <td>1.074380</td>\n",
              "      <td>-0.492199</td>\n",
              "      <td>0.948934</td>\n",
              "      <td>0.428118</td>\n",
              "      <td>1.120631</td>\n",
              "      <td>-3.807864</td>\n",
              "      <td>0.615375</td>\n",
              "      <td>...</td>\n",
              "      <td>1.943465</td>\n",
              "      <td>-1.015455</td>\n",
              "      <td>0.057504</td>\n",
              "      <td>-0.649709</td>\n",
              "      <td>-0.415267</td>\n",
              "      <td>-0.051634</td>\n",
              "      <td>-1.206921</td>\n",
              "      <td>-1.085339</td>\n",
              "      <td>40.80</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>8</th>\n",
              "      <td>7</td>\n",
              "      <td>-0.894286</td>\n",
              "      <td>0.286157</td>\n",
              "      <td>-0.113192</td>\n",
              "      <td>-0.271526</td>\n",
              "      <td>2.669599</td>\n",
              "      <td>3.721818</td>\n",
              "      <td>0.370145</td>\n",
              "      <td>0.851084</td>\n",
              "      <td>-0.392048</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.073425</td>\n",
              "      <td>-0.268092</td>\n",
              "      <td>-0.204233</td>\n",
              "      <td>1.011592</td>\n",
              "      <td>0.373205</td>\n",
              "      <td>-0.384157</td>\n",
              "      <td>0.011747</td>\n",
              "      <td>0.142404</td>\n",
              "      <td>93.20</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "    <tr>\n",
              "      <th>9</th>\n",
              "      <td>9</td>\n",
              "      <td>-0.338262</td>\n",
              "      <td>1.119593</td>\n",
              "      <td>1.044367</td>\n",
              "      <td>-0.222187</td>\n",
              "      <td>0.499361</td>\n",
              "      <td>-0.246761</td>\n",
              "      <td>0.651583</td>\n",
              "      <td>0.069539</td>\n",
              "      <td>-0.736727</td>\n",
              "      <td>...</td>\n",
              "      <td>-0.246914</td>\n",
              "      <td>-0.633753</td>\n",
              "      <td>-0.120794</td>\n",
              "      <td>-0.385050</td>\n",
              "      <td>-0.069733</td>\n",
              "      <td>0.094199</td>\n",
              "      <td>0.246219</td>\n",
              "      <td>0.083076</td>\n",
              "      <td>3.68</td>\n",
              "      <td>0.0</td>\n",
              "    </tr>\n",
              "  </tbody>\n",
              "</table>\n",
              "<p>10 rows × 31 columns</p>\n",
              "</div>\n",
              "      <button class=\"colab-df-convert\" onclick=\"convertToInteractive('df-7da4466b-3fa8-481f-865f-750b4f4d98bf')\"\n",
              "              title=\"Convert this dataframe to an interactive table.\"\n",
              "              style=\"display:none;\">\n",
              "        \n",
              "  <svg xmlns=\"http://www.w3.org/2000/svg\" height=\"24px\"viewBox=\"0 0 24 24\"\n",
              "       width=\"24px\">\n",
              "    <path d=\"M0 0h24v24H0V0z\" fill=\"none\"/>\n",
              "    <path d=\"M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z\"/><path d=\"M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z\"/>\n",
              "  </svg>\n",
              "      </button>\n",
              "      \n",
              "  <style>\n",
              "    .colab-df-container {\n",
              "      display:flex;\n",
              "      flex-wrap:wrap;\n",
              "      gap: 12px;\n",
              "    }\n",
              "\n",
              "    .colab-df-convert {\n",
              "      background-color: #E8F0FE;\n",
              "      border: none;\n",
              "      border-radius: 50%;\n",
              "      cursor: pointer;\n",
              "      display: none;\n",
              "      fill: #1967D2;\n",
              "      height: 32px;\n",
              "      padding: 0 0 0 0;\n",
              "      width: 32px;\n",
              "    }\n",
              "\n",
              "    .colab-df-convert:hover {\n",
              "      background-color: #E2EBFA;\n",
              "      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);\n",
              "      fill: #174EA6;\n",
              "    }\n",
              "\n",
              "    [theme=dark] .colab-df-convert {\n",
              "      background-color: #3B4455;\n",
              "      fill: #D2E3FC;\n",
              "    }\n",
              "\n",
              "    [theme=dark] .colab-df-convert:hover {\n",
              "      background-color: #434B5C;\n",
              "      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);\n",
              "      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));\n",
              "      fill: #FFFFFF;\n",
              "    }\n",
              "  </style>\n",
              "\n",
              "      <script>\n",
              "        const buttonEl =\n",
              "          document.querySelector('#df-7da4466b-3fa8-481f-865f-750b4f4d98bf button.colab-df-convert');\n",
              "        buttonEl.style.display =\n",
              "          google.colab.kernel.accessAllowed ? 'block' : 'none';\n",
              "\n",
              "        async function convertToInteractive(key) {\n",
              "          const element = document.querySelector('#df-7da4466b-3fa8-481f-865f-750b4f4d98bf');\n",
              "          const dataTable =\n",
              "            await google.colab.kernel.invokeFunction('convertToInteractive',\n",
              "                                                     [key], {});\n",
              "          if (!dataTable) return;\n",
              "\n",
              "          const docLinkHtml = 'Like what you see? Visit the ' +\n",
              "            '<a target=\"_blank\" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'\n",
              "            + ' to learn more about interactive tables.';\n",
              "          element.innerHTML = '';\n",
              "          dataTable['output_type'] = 'display_data';\n",
              "          await google.colab.output.renderOutput(dataTable, element);\n",
              "          const docLink = document.createElement('div');\n",
              "          docLink.innerHTML = docLinkHtml;\n",
              "          element.appendChild(docLink);\n",
              "        }\n",
              "      </script>\n",
              "    </div>\n",
              "  </div>\n",
              "  "
            ]
          },
          "metadata": {},
          "execution_count": 24
        }
      ],
      "source": [
        "creditCard = pd.read_csv('./creditcard.csv', sep=',')\n",
        "print(len(creditCard)) #number of rows\n",
        "creditCard.head(10)\n"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "# Check for duplicates\n",
        "duplicates = creditCard.duplicated()\n",
        "\n",
        "# Print the number of duplicates\n",
        "print(\"Number of duplicates:\", duplicates.sum())\n",
        "\n",
        "# Print the rows that are duplicates\n",
        "print(\"Duplicate rows:\")\n",
        "print(creditCard[duplicates])"
      ],
      "metadata": {
        "id": "fanFWDAsB3cT",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "325b1be5-5ea5-4739-b437-48fc454a0a25"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Number of duplicates: 264\n",
            "Duplicate rows:\n",
            "        Time        V1        V2        V3        V4        V5        V6  \\\n",
            "33        26 -0.529912  0.873892  1.347247  0.145457  0.414209  0.100223   \n",
            "35        26 -0.535388  0.865268  1.351076  0.147575  0.433680  0.086983   \n",
            "113       74  1.038370  0.127486  0.184456  1.109950  0.441699  0.945283   \n",
            "114       74  1.038370  0.127486  0.184456  1.109950  0.441699  0.945283   \n",
            "115       74  1.038370  0.127486  0.184456  1.109950  0.441699  0.945283   \n",
            "...      ...       ...       ...       ...       ...       ...       ...   \n",
            "63209  50608 -0.988271 -0.932178  1.717482 -1.694737  0.957518 -1.149287   \n",
            "66601  52127  1.086164  0.093622  0.501010  1.496840 -0.372806 -0.366468   \n",
            "66603  52127  1.121034  0.151243  0.521250  1.482178 -0.355005 -0.372963   \n",
            "68899  53183 -3.294421  2.012037 -0.638100 -1.216179 -1.159612 -1.035571   \n",
            "68901  53183 -2.518832  3.233574 -1.180463 -1.516301 -3.917682  0.839866   \n",
            "\n",
            "             V7        V8        V9  ...       V21       V22       V23  \\\n",
            "33     0.711206  0.176066 -0.286717  ...  0.046949  0.208105 -0.185548   \n",
            "35     0.693039  0.179742 -0.285642  ...  0.049526  0.206537 -0.187108   \n",
            "113   -0.036715  0.350995  0.118950  ...  0.102520  0.605089  0.023092   \n",
            "114   -0.036715  0.350995  0.118950  ...  0.102520  0.605089  0.023092   \n",
            "115   -0.036715  0.350995  0.118950  ...  0.102520  0.605089  0.023092   \n",
            "...         ...       ...       ...  ...       ...       ...       ...   \n",
            "63209  0.649517 -1.029868 -0.496942  ...  0.010248  0.694859 -0.621559   \n",
            "66601  0.083662 -0.018729  0.330979  ... -0.068037 -0.047133 -0.041746   \n",
            "66603  0.059710 -0.014044  0.333105  ... -0.078974 -0.037606 -0.025837   \n",
            "68899 -0.546133  1.577602 -0.240713  ... -0.072616 -0.454834  0.018939   \n",
            "68901  2.027147  1.056862 -0.392995  ... -0.437588 -0.232687  0.239870   \n",
            "\n",
            "            V24       V25       V26       V27       V28  Amount  Class  \n",
            "33     0.001031  0.098816 -0.552904 -0.073288  0.023307    6.14    0.0  \n",
            "35     0.000753  0.098117 -0.553471 -0.078306  0.025427    1.77    0.0  \n",
            "113   -0.626463  0.479120 -0.166937  0.081247  0.001192    1.18    0.0  \n",
            "114   -0.626463  0.479120 -0.166937  0.081247  0.001192    1.18    0.0  \n",
            "115   -0.626463  0.479120 -0.166937  0.081247  0.001192    1.18    0.0  \n",
            "...         ...       ...       ...       ...       ...     ...    ...  \n",
            "63209  0.373755  0.377671 -0.382606 -1.066374 -0.700201   18.00    0.0  \n",
            "66601  0.410568  0.594542 -0.334603  0.032050  0.023400   37.05    0.0  \n",
            "66603  0.409642  0.601170 -0.332678  0.035460  0.020257   19.00    0.0  \n",
            "68899  0.142009 -0.180752  0.673779 -0.152496  0.118241   40.00    0.0  \n",
            "68901  0.181337 -0.081727  0.754145  0.558209 -0.182099  659.00    0.0  \n",
            "\n",
            "[264 rows x 31 columns]\n"
          ]
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "There was 100 duplicates now there is zero after using the drop duplicates."
      ],
      "metadata": {
        "id": "mWlERkr1IU1O"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "creditCard = creditCard.drop_duplicates() ##removing any duplicates\n"
      ],
      "metadata": {
        "id": "atElWoOQH5IP"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "ez2XQL18U2nY"
      },
      "source": [
        "I know from the logitic Regression model that the dataset contains times and 28 different refernces regarding customers card consumptions. Next I will split the data into train & test sets. "
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "XAhgAysqdI73",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "aad5e1a6-84c4-437f-b1c1-72c3cd230b76"
      },
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "Time      0\n",
              "V1        0\n",
              "V2        0\n",
              "V3        0\n",
              "V4        0\n",
              "V5        0\n",
              "V6        0\n",
              "V7        0\n",
              "V8        0\n",
              "V9        0\n",
              "V10       0\n",
              "V11       0\n",
              "V12       0\n",
              "V13       0\n",
              "V14       1\n",
              "V15       1\n",
              "V16       1\n",
              "V17       1\n",
              "V18       1\n",
              "V19       1\n",
              "V20       1\n",
              "V21       1\n",
              "V22       1\n",
              "V23       1\n",
              "V24       1\n",
              "V25       1\n",
              "V26       1\n",
              "V27       1\n",
              "V28       1\n",
              "Amount    1\n",
              "Class     1\n",
              "dtype: int64"
            ]
          },
          "metadata": {},
          "execution_count": 27
        }
      ],
      "source": [
        "creditCard.isnull().sum() ##checking to see if theres missing values in the dataset"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "ffH2dRjBdonY"
      },
      "outputs": [],
      "source": [
        "creditCard = creditCard.dropna() ##CreditCard \n",
        "##DataFrame has missing values, I can remove the rows containing missing values \n",
        "##using dropna method of Pandas DataFrame. \n"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "Ufb4QhnGd-An",
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "outputId": "50a17cab-7967-4dc8-c15d-03489141791e"
      },
      "outputs": [
        {
          "output_type": "execute_result",
          "data": {
            "text/plain": [
              "Time      0\n",
              "V1        0\n",
              "V2        0\n",
              "V3        0\n",
              "V4        0\n",
              "V5        0\n",
              "V6        0\n",
              "V7        0\n",
              "V8        0\n",
              "V9        0\n",
              "V10       0\n",
              "V11       0\n",
              "V12       0\n",
              "V13       0\n",
              "V14       0\n",
              "V15       0\n",
              "V16       0\n",
              "V17       0\n",
              "V18       0\n",
              "V19       0\n",
              "V20       0\n",
              "V21       0\n",
              "V22       0\n",
              "V23       0\n",
              "V24       0\n",
              "V25       0\n",
              "V26       0\n",
              "V27       0\n",
              "V28       0\n",
              "Amount    0\n",
              "Class     0\n",
              "dtype: int64"
            ]
          },
          "metadata": {},
          "execution_count": 29
        }
      ],
      "source": [
        "creditCard.isnull().sum() ##currently no missing values now"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "Z_V5mx-vVSy9"
      },
      "outputs": [],
      "source": [
        "array = creditCard.values\n",
        "\n",
        "X = array[:,0:30] #X = array[:,0:30] takes the first 30 columns of the  \n",
        "                  #NumPy array array and assigns them to a variable X as the features of the model.\n",
        "Y = array[:,30] #Y takes the 31st column\n",
        "\n",
        "X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size=0.3, stratify=Y)"
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "PXJD_PZPeIRL"
      },
      "source": [
        "Feature Selection (Importance)\n",
        "Tree-based estimators (see the sklearn.tree module and forest of trees in the sklearn.ensemble module) can be used to compute impurity-based feature importances, which in turn can be used to discard irrelevant features (when coupled with the SelectFromModel meta-transformer):\n",
        "\n",
        "\n"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "nr_7K4UYeNKR",
        "outputId": "fda9b808-48be-4bc2-d5ab-42aae40bab44"
      },
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "[0.01976566 0.01457951 0.02747335 0.02620575 0.04141513 0.01494904\n",
            " 0.01527433 0.05573214 0.01900051 0.08672744 0.06222464 0.0700188\n",
            " 0.08286657 0.01035308 0.08931314 0.01202651 0.05867103 0.08818671\n",
            " 0.05260742 0.01493632 0.01283597 0.02290562 0.01091303 0.00712123\n",
            " 0.01043503 0.0067265  0.02322548 0.01429944 0.01335195 0.01585867]\n"
          ]
        }
      ],
      "source": [
        "# feature extraction\n",
        "clf = ExtraTreesClassifier(n_estimators=10)\n",
        "clf.fit(X, Y)\n",
        "print(clf.feature_importances_)"
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "qNWQO2TXhU79"
      },
      "source": [
        "Models - Random Forest, K-Nearest-Neighbor, and Logistic Regression. These models will be tested using sklearn library.  X and y receive new values, based on the most representative variables found by Feature Selection."
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "lhUfI8vDhXcr"
      },
      "outputs": [],
      "source": [
        "array = creditCard.values\n",
        "X = array[:, [17,14,10,12,11]]\n",
        "Y = array[:,30]\n",
        "X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size=0.3, stratify=Y)"
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "XMTz-0MPkaxU"
      },
      "source": [
        "Creating the knn model and testing it. GridSearch is used to test all values for n_neighbors, and finally we fit the model to the training data."
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "IDTuSpZ7ki5D",
        "outputId": "352fde79-76f8-4b73-fd1f-f729b1f02eeb"
      },
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "CPU times: user 1.91 s, sys: 318 ms, total: 2.23 s\n",
            "Wall time: 2min 47s\n"
          ]
        }
      ],
      "source": [
        "knn = KNeighborsClassifier(algorithm='auto')\n",
        "params_knn = {\n",
        "    'n_neighbors': (1,30, 1),\n",
        "    'leaf_size': (20,40,1),\n",
        "    'p': (1,2),\n",
        "    'weights': ('uniform', 'distance'),\n",
        "    'metric': ('minkowski', 'chebyshev')}\n",
        "knn_gs = GridSearchCV(\n",
        "    estimator=knn,\n",
        "    param_grid=params_knn,\n",
        "    scoring = 'accuracy',\n",
        "    n_jobs = -1,\n",
        "    cv = 5)\n",
        "\n",
        "%time knn_gs.fit(X_train, y_train)\n",
        "y_pred = knn_gs.predict(X_test)"
      ]
    },
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "UJtHc4dGlicf"
      },
      "source": [
        "the best model is saved and the best parameters are shown, together with model's Area Under the Curve (AUC)"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "QImI-KiDlhUM",
        "outputId": "b81ea700-660a-4ccb-f1d5-ee0676a0e605"
      },
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "{'leaf_size': 20, 'metric': 'minkowski', 'n_neighbors': 30, 'p': 1, 'weights': 'distance'}\n"
          ]
        }
      ],
      "source": [
        "knn_best = knn_gs.best_estimator_\n",
        "print(knn_gs.best_params_)\n",
        "fpr, tpr, _ = roc_curve(y_test, y_pred)\n",
        "\n",
        "knn_auc = auc(fpr, tpr)"
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "Random Forest. I will be creating a Random forest classifier, as I want to test the values. I will be using gridsearch to test the values. "
      ],
      "metadata": {
        "id": "m1UhaAR9k4dN"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "rf = RandomForestClassifier()\n",
        "params_rf = {\"n_estimators\": np.arange(1,30,1)}\n",
        "rf_gs = GridSearchCV(rf, params_rf, cv=5)\n",
        "\n",
        "%time rf_gs.fit(X_train, y_train)\n",
        "y_pred = rf_gs.predict(X_test)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "niVtRNS5l1n8",
        "outputId": "98dfffd9-e7b7-45fd-c281-942e28d96d69"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "CPU times: user 2min 33s, sys: 366 ms, total: 2min 34s\n",
            "Wall time: 2min 35s\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "rf_best = rf_gs.best_estimator_\n",
        "print(rf_gs.best_params_)\n",
        "\n",
        "fpr, tpr, _ = roc_curve(y_test, y_pred)\n",
        "\n",
        "rf_auc = auc(fpr, tpr) "
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "-zP2s_40mBbM",
        "outputId": "d5e42a9b-a81c-4fcb-d2c0-8435a7353b13"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "{'n_estimators': 16}\n"
          ]
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "Logistic Regression"
      ],
      "metadata": {
        "id": "7HcgrEQS8ENI"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "log_reg = LogisticRegression(max_iter=1000)\n",
        "%time log_reg.fit(X_train, y_train)\n",
        "y_pred = log_reg.predict(X_test)"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "rwbhAdIG781P",
        "outputId": "35badfb4-a175-4d92-bcfa-6207be3f1c37"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "CPU times: user 135 ms, sys: 41.1 ms, total: 176 ms\n",
            "Wall time: 166 ms\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "fpr, tpr, _ = roc_curve(y_test, y_pred)\n",
        "\n",
        "lr_auc = auc(fpr, tpr)"
      ],
      "metadata": {
        "id": "oiARZ3ZS8PQq"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "Ensemble - The whole purpose of this project to combine the supervised algorithms. Voting/stacking classifiers."
      ],
      "metadata": {
        "id": "LRmmLsM0oQXH"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "estimators=[(\"knn\", knn_best), (\"rf\", rf_best), (\"log_reg\", log_reg)]"
      ],
      "metadata": {
        "id": "2LGddzZeo_hi"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "Now Voting Classifier. Trains on an ensemble of numerous models and predicts an output (class) based on their highest probability of chosen class as the output.\n"
      ],
      "metadata": {
        "id": "0mmmz4OkwLm0"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "ensemble_class = VotingClassifier(estimators, voting=\"hard\")\n",
        "\n",
        "ensemble_class.fit(X_train, y_train)\n",
        "\n",
        "ensemble_class.score(X_test, y_test)\n",
        "y_pred = ensemble_class.predict(X_test)"
      ],
      "metadata": {
        "id": "VLQrkTMtwUsb"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "best model is saved and the best parameters are shown, together with model's Area Under the Curve."
      ],
      "metadata": {
        "id": "59mIQdtBx9Ik"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "fpr, tpr, _ = roc_curve(y_test, y_pred)\n",
        "\n",
        "ensemble_class_auc = auc(fpr, tpr) "
      ],
      "metadata": {
        "id": "wYbFWCLQx4cw"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "Stacking Classifier - combines multiple classification models to create one “super” model"
      ],
      "metadata": {
        "id": "tSxkpsymyKW0"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "ensemble_stack = StackingClassifier(estimators=estimators)\n",
        "\n",
        "ensemble_stack.fit(X_train, y_train)\n",
        "\n",
        "ensemble_stack.score(X_test, y_test)\n",
        "y_pred = ensemble_stack.predict(X_test)"
      ],
      "metadata": {
        "id": "u7qK6aH_yDrT"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "fpr, tpr, _ = roc_curve(y_test, y_pred)\n",
        "\n",
        "ensemble_stack_auc = auc(fpr, tpr)"
      ],
      "metadata": {
        "id": "348oafZpyPP4"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "Model Metrics to see the results shown"
      ],
      "metadata": {
        "id": "ATMfSJhGyZYu"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "print(\"KNN Score: {}\".format(knn_best.score(X_test, y_test)))\n",
        "print(\"RF Score: {}\".format(rf_best.score(X_test, y_test)))\n",
        "print(\"Log_Reg Score: {}\".format(log_reg.score(X_test, y_test)))\n",
        "print(\"Ensemble Voting Score: {}\".format(ensemble_class.score(X_test, y_test)))\n",
        "print(\"Ensemble Stacking Score: {}\".format(ensemble_stack.score(X_test, y_test)))"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "eJi3pz0JydDy",
        "outputId": "29ad644e-bd20-4260-86fb-70ff1412246e"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "KNN Score: 0.9993733429742107\n",
            "RF Score: 0.9994215473608098\n",
            "Log_Reg Score: 0.9983610508556279\n",
            "Ensemble Voting Score: 0.9993251385876115\n",
            "Ensemble Stacking Score: 0.9993733429742107\n"
          ]
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        " using AUC, it is possible to see that the objective was achieved: to find a percentage of precision using only the variables chosen by the Feature Selection (which possibly also decreased overfitting). "
      ],
      "metadata": {
        "id": "UE72lvU9y0yu"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "print(\"KNN AUC: {}\".format(knn_auc))\n",
        "print(\"RF AUC: {}\".format(rf_auc))\n",
        "print(\"Log_Reg AUC: {}\".format(lr_auc))\n",
        "print(\"Ensemble Voting AUC: {}\".format(ensemble_class_auc))\n",
        "print(\"Ensemble Stacking AUC: {}\".format(ensemble_stack_auc))"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "qVeDG12Sy4jB",
        "outputId": "846213b1-574d-4c02-e98a-2e7a2b438fd6"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "KNN AUC: 0.9037736655650926\n",
            "RF AUC: 0.9037978283254464\n",
            "Log_Reg AUC: 0.7402154760621392\n",
            "Ensemble Voting AUC: 0.8845670590946771\n",
            "Ensemble Stacking AUC: 0.875\n"
          ]
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "Now I will plot the results."
      ],
      "metadata": {
        "id": "VvJEhewlzQH3"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "\n",
        "fig = plt.figure()\n",
        "ax = fig.add_axes([0,0,1,1])\n",
        "models = ['KNN', 'RF', 'Log_Reg', 'Voting', 'Stacking']\n",
        "values = [knn_auc, rf_auc, lr_auc, ensemble_class_auc, ensemble_stack_auc]\n",
        "ax.bar(models, values)\n",
        "plt.show()"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 337
        },
        "id": "srx-62b4zTRv",
        "outputId": "d61f91f7-c212-4ea0-ddbd-f9f0fc2170a0"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "<Figure size 432x288 with 1 Axes>"
            ],
            "image/png": "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\n"
          },
          "metadata": {
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "markdown",
      "source": [
        "Confussion Metrics"
      ],
      "metadata": {
        "id": "gnsmyHWZzfNm"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "plot_confusion_matrix(knn_gs, X_test, y_test)  \n",
        "plt.show() "
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 334
        },
        "id": "T5ru2SzLziwq",
        "outputId": "01f53996-5048-4475-d965-848ade63610c"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.8/dist-packages/sklearn/utils/deprecation.py:87: FutureWarning: Function plot_confusion_matrix is deprecated; Function `plot_confusion_matrix` is deprecated in 1.0 and will be removed in 1.2. Use one of the class methods: ConfusionMatrixDisplay.from_predictions or ConfusionMatrixDisplay.from_estimator.\n",
            "  warnings.warn(msg, category=FutureWarning)\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "<Figure size 432x288 with 2 Axes>"
            ],
            "image/png": "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\n"
          },
          "metadata": {
            "needs_background": "light"
          }
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "plot_confusion_matrix(rf_gs, X_test, y_test)  \n",
        "plt.show() "
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/",
          "height": 334
        },
        "id": "99KghxUTKcnQ",
        "outputId": "a1634648-6f5d-4d31-cdf0-0009502679f4"
      },
      "execution_count": null,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stderr",
          "text": [
            "/usr/local/lib/python3.8/dist-packages/sklearn/utils/deprecation.py:87: FutureWarning: Function plot_confusion_matrix is deprecated; Function `plot_confusion_matrix` is deprecated in 1.0 and will be removed in 1.2. Use one of the class methods: ConfusionMatrixDisplay.from_predictions or ConfusionMatrixDisplay.from_estimator.\n",
            "  warnings.warn(msg, category=FutureWarning)\n"
          ]
        },
        {
          "output_type": "display_data",
          "data": {
            "text/plain": [
              "<Figure size 432x288 with 2 Axes>"
            ],
            "image/png": "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\n"
          },
          "metadata": {
            "needs_background": "light"
          }
        }
      ]
    }
  ],
  "metadata": {
    "colab": {
      "provenance": [],
      "include_colab_link": true
    },
    "kernelspec": {
      "display_name": "Python 3",
      "name": "python3"
    },
    "language_info": {
      "name": "python"
    }
  },
  "nbformat": 4,
  "nbformat_minor": 0
}