# Adding New Blogs to Ethereal Blog Landing

This guide explains how to add new blog posts to the ethereal-blog-landing repository.

## New Workflow (October 2025)

All blog posts are now stored **locally** in this repository instead of in separate GitHub repositories. This simplifies management and keeps everything in one place.

## Directory Structure

```
ethereal-blog-landing/
├── blogs/                    # All blog HTML files stored here
├── assets/images/           # Preview/hero images for landing page
├── data/blogs.json         # Blog metadata for the landing page
└── index.html              # Landing page that displays all blogs
```

## Step-by-Step Process

### 1. Copy Blog HTML File

Copy the new blog HTML file to the `blogs/` folder:

```bash
cp "/path/to/your-new-blog.html" /home/lknwake50/ethereal-blog-landing/blogs/
```

### 2. Extract Hero Image

Since all blogs have embedded base64 images, extract the first (hero) image:

```bash
cd /home/lknwake50/ethereal-blog-landing

python3 << 'PYTHON_EOF'
import re
import base64
import sys

# Get blog filename from command line or use default
blog_file = sys.argv[1] if len(sys.argv) > 1 else 'your-blog-filename.html'
image_name = sys.argv[2] if len(sys.argv) > 2 else 'hero-image.jpg'

# Read the HTML file
with open(f'blogs/{blog_file}', 'r', encoding='utf-8') as f:
    content = f.read()

# Find the first image (hero image)
match = re.search(r'src="data:image/(jpeg|jpg|png);base64,([^"]+)"', content)

if match:
    image_type = match.group(1)
    base64_data = match.group(2)

    # Decode and save
    image_data = base64.b64decode(base64_data)

    # Save to assets/images
    filename = f'assets/images/{image_name}'
    with open(filename, 'wb') as img_file:
        img_file.write(image_data)

    print(f"✓ Extracted hero image: {filename}")
    print(f"  Image size: {len(image_data):,} bytes")
else:
    print("✗ No embedded image found")
    sys.exit(1)
PYTHON_EOF
```

**Example:**
```bash
python3 extract_hero.py your-blog-filename.html your-hero-image.jpg
```

### 3. Update blogs.json

Add a new entry at the **top** of the `data/blogs.json` array:

```json
{
  "id": "unique-blog-id",
  "title": "Your Blog Title Here",
  "url": "./blogs/your-blog-filename.html",
  "linkUrl": "./blogs/your-blog-filename.html",
  "readMoreUrl": "./blogs/your-blog-filename.html",
  "imageUrl": "./assets/images/your-hero-image.jpg",
  "summary": "A brief description of the blog post (1-2 sentences)",
  "tags": ["Category1", "Category2", "North Carolina"],
  "author": "Bill Layne",
  "date": "2025-10-13",
  "category": "Community Life",
  "readTime": "8 min read",
  "featured": true
}
```

#### Field Descriptions:

- **id**: Unique identifier (lowercase-with-dashes)
- **title**: Full blog title as it appears in the HTML
- **url/linkUrl/readMoreUrl**: All point to `./blogs/filename.html`
- **imageUrl**: Path to extracted hero image `./assets/images/name.jpg`
- **summary**: Extract from blog meta description or write brief summary
- **tags**: Related topics (always include "North Carolina")
- **author**: "Bill Layne"
- **date**: Publication date (YYYY-MM-DD)
- **category**: One of:
  - Community Life
  - Auto Insurance
  - Home Insurance
  - Financial Planning
  - General
- **readTime**: Estimate (e.g., "8 min read")
- **featured**: `true` for new posts, can be changed later

### 4. Extract Blog Metadata

To get the blog title and description, check the HTML file:

```bash
# Get title
grep -E "<title>" blogs/your-blog-filename.html | sed 's/<[^>]*>//g'

# Get description
grep -E '("description"|og:description)' blogs/your-blog-filename.html | head -1
```

### 5. Commit and Push

```bash
cd /home/lknwake50/ethereal-blog-landing

# Add all changes
git add blogs/ assets/images/ data/blogs.json

# Commit with descriptive message
git commit -m "Add new blog: Your Blog Title

- Added blog HTML to blogs/ folder
- Extracted and saved hero image
- Updated blogs.json with blog metadata

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"

# Push to GitHub
git push
```

## Quick Checklist

- [ ] Copy blog HTML to `blogs/` folder
- [ ] Extract hero image to `assets/images/`
- [ ] Add entry to top of `data/blogs.json`
- [ ] Verify all paths are relative (`./blogs/...` and `./assets/images/...`)
- [ ] Commit changes with descriptive message
- [ ] Push to GitHub
- [ ] Verify blog appears on landing page

## Common Categories

- **Community Life**: Local events, entertainment, seasonal guides
- **Auto Insurance**: Car insurance topics, NC driving laws
- **Home Insurance**: Homeowners insurance, property protection
- **Financial Planning**: Financial advice, money management
- **General**: Mixed topics or multiple categories

## Tips

1. **Always add new blogs to the top** of blogs.json array
2. **Use descriptive IDs** that match the blog topic
3. **Keep summaries concise** (1-2 sentences max)
4. **Use consistent date format** (YYYY-MM-DD)
5. **Test locally** before pushing if possible
6. **Featured posts** appear first on the landing page

## Automated Script (Future)

You can create a helper script to automate this process:

```bash
#!/bin/bash
# add-blog.sh

BLOG_FILE="$1"
BLOG_NAME=$(basename "$BLOG_FILE")
HERO_IMAGE_NAME="${BLOG_NAME%.html}-hero.jpg"

# Copy blog
cp "$BLOG_FILE" blogs/

# Extract hero image
python3 extract_hero.py "$BLOG_NAME" "$HERO_IMAGE_NAME"

echo "✓ Blog added: blogs/$BLOG_NAME"
echo "✓ Hero image: assets/images/$HERO_IMAGE_NAME"
echo ""
echo "Next steps:"
echo "1. Update data/blogs.json with blog metadata"
echo "2. Commit and push changes"
```

## Need Help?

If you encounter issues:
1. Check that file paths are correct
2. Ensure images are properly extracted
3. Validate blogs.json syntax (JSON must be valid)
4. Check git status before committing

---

**Last Updated:** October 13, 2025
