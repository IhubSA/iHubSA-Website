# iHubSA Website

Professional, high-converting website for iHubSA - transforming business processes into digital solutions.

## 🚀 Quick Start

### Local Testing
Simply open `index.html` in your web browser. No build process or local server required.

```bash
# On macOS
open index.html

# On Windows
start index.html

# On Linux
xdg-open index.html
```

### Deploy to GitHub Pages

#### Step 1: Create a GitHub Repository
1. Go to [github.com/new](https://github.com/new)
2. Name it: `ihubsa-website` (or your preferred name)
3. Add description: "iHubSA - Business Digitalisation & SaaS Solutions"
4. Choose "Public"
5. Click "Create repository"

#### Step 2: Upload Files
1. Click "Add file" → "Upload files"
2. Drag and drop all files:
   - `index.html`
   - `README.md`
   - `robots.txt`
   - `sitemap.xml`
3. Click "Commit changes"

#### Step 3: Enable GitHub Pages
1. Go to repository "Settings"
2. Navigate to "Pages" (left sidebar)
3. Under "Build and deployment":
   - Source: Select "Deploy from a branch"
   - Branch: Select "main"
   - Folder: Select "/ (root)"
4. Click "Save"
5. Wait 1-2 minutes for deployment

Your site will be available at: `https://yourusername.github.io/ihubsa-website`

### Update the Website

To make changes:
1. Edit `index.html` in your text editor
2. Commit and push to GitHub
3. Changes automatically deploy (takes ~1 minute)

```bash
# Using Git command line (optional)
git add index.html
git commit -m "Update website content"
git push origin main
```

## 📋 File Structure

```
ihubsa-website/
├── index.html          # Main website (all HTML, CSS, JS)
├── README.md          # This file
├── robots.txt         # Search engine crawling rules
├── sitemap.xml        # Site map for SEO
└── assets/            # (Optional) For images, fonts if needed
```

## 🎨 Customization

### Brand Colors
Edit the CSS variables in the `<style>` section:
```css
:root {
    --primary-blue: #073F68;      /* Main brand color */
    --primary-orange: #FF7012;    /* Accent color */
}
```

### Contact Information
Replace placeholders in footer and form:
- `[COMPANY_EMAIL]` → your email
- `[PHONE_NUMBER]` → your phone
- `[ADDRESS]` → your address

### Company Name / Branding
Search for "iHubSA" in the file and update as needed.

### Content Updates
- Edit section headings, descriptions, and copy directly in the HTML
- All content is in the `<body>` section
- Keep the semantic structure (h2 for section titles, etc.)

## 🔧 Future Supabase Integration

The form is structured to support backend integration:

### Step 1: Set Up Supabase
1. Create a project at [supabase.com](https://supabase.com)
2. Create a `leads` table with columns:
   - `id` (auto)
   - `name` (text)
   - `company` (text)
   - `email` (text)
   - `phone` (text)
   - `industry` (text)
   - `current_systems` (text)
   - `problem` (text)
   - `build_options` (jsonb)
   - `created_at` (timestamp)

### Step 2: Create Edge Function (Backend)
Create a Supabase Edge Function to handle form submissions:

```bash
supabase functions new handle-lead-form
```

### Step 3: Update Form Submission
Replace the `handleFormSubmit` function with:

```javascript
async function handleFormSubmit(event) {
    event.preventDefault();
    
    const formData = {
        name: document.getElementById('name').value,
        company: document.getElementById('company').value,
        email: document.getElementById('email').value,
        phone: document.getElementById('phone').value,
        industry: document.getElementById('industry').value,
        current_systems: document.getElementById('current-systems').value,
        problem: document.getElementById('problem').value,
        build_options: Array.from(document.querySelectorAll('input[name="build"]:checked')).map(cb => cb.value)
    };

    try {
        const response = await fetch(
            'https://your-project.supabase.co/functions/v1/handle-lead-form',
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer YOUR_ANON_KEY`
                },
                body: JSON.stringify(formData)
            }
        );

        const result = await response.json();
        alert('Thank you! We\'ll be in touch within 24 hours.');
        document.getElementById('leadForm').reset();
    } catch (error) {
        console.error('Error:', error);
        alert('There was an error. Please try again or contact us directly.');
    }
}
```

### Step 4: Environment Variables
Create a `.env.local` file (never commit this):
```
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
```

**Important:** Never expose your Supabase URL or API keys directly in client-side code. Use a backend function or middleware to handle database operations securely.

## 📱 Responsive Design

The website is fully responsive:
- **Desktop** (1200px+): Full layout
- **Tablet** (768px - 1199px): Optimized for medium screens
- **Mobile** (<768px): Mobile-first design, hamburger menu

Test on different devices:
- Chrome DevTools: F12 → Toggle device toolbar (Ctrl+Shift+M)
- Safari: Develop → Enter Responsive Design Mode (Cmd+Ctrl+R)

## 🔍 SEO Features

- ✅ Meta title and description
- ✅ Open Graph tags
- ✅ Semantic HTML (H1, H2, H3, etc.)
- ✅ robots.txt for crawlers
- ✅ sitemap.xml for indexing
- ✅ Responsive design (mobile-friendly)
- ✅ Fast load times (no external dependencies)

### Submit to Search Engines
1. **Google**: [Search Console](https://search.google.com/search-console/)
2. **Bing**: [Webmaster Tools](https://www.bing.com/webmasters/)

## 🐛 Troubleshooting

### Site not loading on GitHub Pages
- Ensure `index.html` is in the root directory
- Check GitHub Pages settings (Settings → Pages)
- Clear browser cache (Ctrl+Shift+Delete)
- Wait 2-3 minutes for deployment

### Relative paths not working
- All links use relative paths automatically
- If deploying to a subdirectory, check repository name matches URL

### Form not submitting
- Check browser console for errors (F12)
- Verify all required fields are filled
- For backend integration, check API endpoint and keys

## 📊 Analytics (Optional)

Add Google Analytics:

```html
<!-- Add before closing </head> tag -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>
```

Replace `GA_MEASUREMENT_ID` with your Google Analytics ID.

## 📝 License

© 2026 iHubSA. All rights reserved.

## 🤝 Support

For questions or issues:
- Email: [COMPANY_EMAIL]
- Phone: [PHONE_NUMBER]

---

**Built with HTML5, CSS3, and vanilla JavaScript. No external dependencies. Perfect for GitHub Pages.**
