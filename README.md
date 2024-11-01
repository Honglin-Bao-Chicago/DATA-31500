# NYC Illegal Animals as Pets Data Visualization

Explore the prevalence of illegal pets across New York City with an interactive visualization, organized by census regions. Built with **D3.js** and **Svelte**, this app provides an engaging experience to explore data by **City** and **Borough**, focusing on various types of animals.

## Features

- **Interactive Choropleth Map**: A color-coded map of NYC shows the distribution of illegal animals by **Borough**, making it easy to identify regions with higher incident counts.
- **Top 10 City Histogram**: A histogram highlights the top 10 NYC cities by number of incidents, giving insights into areas with the highest occurrences of illegal pets.
- **Dynamic Filtering**: Users can select ranges within the histogram to refine the map and histogram views, allowing a detailed analysis of specific areas and animal types.
- **Adaptive Legend**: The map legend dynamically adjusts to reflect the data distribution, providing clear visual cues for each borough’s illegal pet count.


## Data

This dataset includes details on illegal pets across NYC, broken down by city and borough. Key data points include:
- **City and Borough Distribution**: Incident counts organized by city and borough.
- **Animal Types**: Categories such as Snake, Rooster, and Farm Animal are included.

## Technologies Used

- **Svelte**: For a responsive, interactive user interface.
- **D3.js**: For data scaling, color schemes, and document manipulation.
- **GeoJSON**: For mapping NYC census regions in the interactive map.
