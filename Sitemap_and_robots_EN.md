# Sitemap Generation Script

This script generates a sitemap for the <your_site> website, aggregating various URLs and their last modified dates for better search engine optimization (SEO) and web crawling purposes.

## Structure and Overview

- **Base URL**: `https://<your_site>`

The script begins by retrieving data for the main page, extracting sections, header links, and footer links. These elements form the fundamental structure of the website.

- **Latest News URLs**: Extracts the latest news URLs from the API and formats them.

- **Procedure URLs**: Fetches procedure details and formats their URLs accordingly.

- **Formulars URLs**:
  - Obtains formular details and processes their document sections to create formatted URLs.

- **Thematic Sections and Articles**:
  - Retrieves thematic sections and their articles.
  - Generates URLs for thematic sections and their respective articles.

- **Privacy and Sale Terms URLs**:
  - Appends URLs for the privacy policy and sale terms.

The script handles these sections, formats their URLs, and creates a sitemap with their respective last modified dates in the YYYY-MM-DD format.

## Important Notes

- The script uses Moment.js to format the last modified dates.
- Certain sections like legislations and sponsor-related URLs are currently commented out and not included in the sitemap.


# Robots.txt Configuration for <your_site>

This `robots.txt` configuration is designed for the <your_site> website, specifying directives for web crawlers and search engine robots.

## Rules Overview

- **User Agent**: `*` (Applies to all robots)
  - `allow`: Specifies the allowed paths for crawling.
    - `/`: Allows crawling of the website's root.
  - `disallow`: Prevents crawling specific paths.
    - `/private/`: Disallows crawling of the `/private/` directory or its contents.

## Sitemap Declaration

- **Sitemap**: `https://<your_site>/sitemap.xml`
  - Directs crawlers to the location of the website's sitemap, aiding in indexing and discovering website URLs efficiently.

This `robots.txt` file ensures that web crawlers follow the defined rules when indexing the <your_site> website, allowing access to certain sections while restricting access to others. It also provides the sitemap location for better navigation and indexing of the website's URLs.
