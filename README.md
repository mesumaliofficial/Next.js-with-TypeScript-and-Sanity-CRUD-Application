Next.js with TypeScript and Sanity CRUD Application

Yeh ek simple blog post management app hai jo Next.js aur TypeScript ke sath bani hai aur Sanity ko backend CMS ke tor par use karti hai. Isme aap blog posts ko create, read, update, aur delete kar sakte hain. Har line ke aage Roman Urdu mein comments hain jo itne asaan hain ke koi bhi beginner 2 minute mein samajh jaye. Yeh guide batayega ke konsi file kahan rakhni hai aur konsa code kahan likhna hai.
Yeh App Kya Karti Hai?

Homepage par sab posts ki list dikhai deti hai.
Nayi post create karne ka form hai.
Kisi post ko edit karne ka option hai.
Sanity Studio se posts delete kar sakte hain.

Prerequisites (Pehle Yeh Chahiye)

Node.js: Version 18 ya usse zyada (Next.js aur Sanity CLI chalane ke liye).
npm: Node.js ke sath aata hai (packages install karne ke liye).
Sanity Account: sanity.io par account banayein aur project ID aur API token lein.
Git: Repository clone karne ke liye.

Project Structure (Files Kahan Hain)
Yeh project is tarah se organized hai:

/pages: Frontend pages ke liye Next.js files.
index.tsx: Homepage jo posts ki list dikhata hai.
create.tsx: Nayi post banane ka page.
edit/[id].tsx: Kisi post ko edit karne ka page.


/lib: Sanity ke connection aur queries ke liye.
sanityClient.ts: Sanity se baat karne ka setup.


/sanity: Sanity Studio aur schemas ke liye.
sanity.config.ts: Sanity Studio ka configuration.
schemas/post.ts: Post ka data structure.


/components: Reusable React components.
PostForm.tsx: Post create aur edit karne ka form.


/types: TypeScript ke liye data types.
index.ts: Post ka TypeScript interface.


.env.local: Environment variables (git mein nahi daalte).
next.config.mjs: Next.js ka configuration.
package.json: Project ke dependencies aur scripts.
tsconfig.json: TypeScript ka configuration.

Setup Instructions (Kaise Setup Karein)
In steps ko follow karein:

Repository Clone Karein
git clone <repository-url>
cd <repository-folder>


Yeh command project ko aapke computer par download karegi aur us folder mein le jayegi.


Dependencies Install Karein
npm install


Yeh Next.js, TypeScript, Sanity, aur doosre packages install karega.


Environment Variables Set Karein

Project ke root folder mein ek .env.local file banayein.
Isme yeh variables daalein (apna Sanity project ID aur token sanity.io/manage se lein):NEXT_PUBLIC_SANITY_PROJECT_ID=<your-project-id>
NEXT_PUBLIC_SANITY_DATASET=production
NEXT_PUBLIC_SANITY_API_TOKEN=<your-api-token>


Yeh variables app ko Sanity se connect karte hain. Token create, update, delete ke liye chahiye.




Sanity Studio Set Karein

/sanity folder mein jayein:cd sanity
npm install


Sanity Studio chalayein:npm run dev


Browser mein http://localhost:3333 kholein, yahan aap data manage kar sakte hain.


Next.js App Chalayein

Ek naya terminal kholein, project root mein wapas jayein:cd ..
npm run dev


Yeh app ko http://localhost:3000 par chalayega.


Check Karein

http://localhost:3000 par homepage dekhein.
http://localhost:3333 par Sanity Studio dekhein.
Studio mein ek post banayein aur check karein ke homepage par dikh rahi hai.



Key Files aur Code Snippets
Neeche har important file ka code hai, har line ke aage Roman Urdu mein comment hai, aur yeh bataya gaya hai ke file kahan rakhni hai.
1. File: lib/sanityClient.ts
Kahan Rakhna Hai: /lib folder ke andar ek file sanityClient.ts banayein.Kya Karta Hai: Yeh Sanity ke sath connection banata hai taake hum posts fetch aur save kar sakein.
// Sanity ka client import karein jo API se baat karega
import sanityClient from '@sanity/client';

// Sanity client banayein jo API calls ke liye use hoga
export const client = sanityClient({
  projectId: process.env.NEXT_PUBLIC_SANITY_PROJECT_ID, // .env.local se project ID lein
  dataset: process.env.NEXT_PUBLIC_SANITY_DATASET, // .env.local se dataset lein (jaise 'production')
  apiVersion: '2023-10-01', // API ka version set karein taake stable rahe
  token: process.env.NEXT_PUBLIC_SANITY_API_TOKEN, // .env.local se token lein jo write access deta hai
  useCdn: false, // CDN band karein taake fresh data mile (thoda slow hoga)
});


Kyun: Yeh file Sanity se data lene aur save karne ke liye zaruri hai.
Kahan Use Hoga: Posts fetch aur save karne ke liye doosre components mein.

2. File: types/index.ts
Kahan Rakhna Hai: /types folder ke andar ek file index.ts banayein.Kya Karta Hai: Yeh TypeScript ko batata hai ke post ka data kaisa dikhega.
// Post ka structure define karein taake TypeScript errors na de
export interface Post {
  _id: string; // Har post ka unique ID jo Sanity deta hai
  title: string; // Post ka title
  content: string; // Post ka main text
  _createdAt: string; // Post kab bani, Sanity se milta hai
}


Kyun: Yeh ensure karta hai ke humara data hamesha sahi format mein ho.
Kahan Use Hoga: Pages aur components mein jab posts ke sath kaam karenge.

3. File: pages/index.tsx
Kahan Rakhna Hai: /pages folder ke andar ek file index.tsx banayein.Kya Karta Hai: Yeh homepage hai jo sab posts ki list dikhata hai aur create/edit ke links deta hai.
// Next.js ka Link component import karein jo pages ke beech navigate karta hai
import Link from 'next/link';
// React ke hooks import karein state aur lifecycle manage karne ke liye
import { useEffect, useState } from 'react';
// Sanity client import karein data fetch karne ke liye
import { client } from '../lib/sanityClient';
// Post ka type import karein taake TypeScript errors na de
import { Post } from '../types';

// Homepage component banayein
export default function Home() {
  // Posts ki list store karne ke liye state banayein
  const [posts, setPosts] = useState<Post[]>([]);

  // Jab page load ho, posts fetch karein
  useEffect(() => {
    // Async function banayein jo Sanity se posts le
    const fetchPosts = async () => {
      // Query jo sab posts select karegi
      const query = `*[_type == "post"]`;
      // Sanity se data fetch karein
      const data = await client.fetch(query);
      // Fetched data ko state mein save karein
      setPosts(data);
    };
    // Fetch function call karein
    fetchPosts();
  }, []); // Empty array ka matlab yeh sirf ek baar chalega jab page load ho

  // Page ka HTML render karein
  return (
    // Main container jo page ko center mein rakhega
    <div className="container mx-auto p-4">
      // Page ka title
      <h1 className="text-3xl font-bold mb-4">Blog Posts</h1>
      // Create post ka link
      <Link href="/create" className="text-blue-500 hover:underline">
        Nayi Post Banayein
      </Link>
      // Posts ki list
      <ul className="mt-4">
        // Har post ke liye loop chalayein
        {posts.map((post) => (
          // Har list item ka unique key set karein
          <li key={post._id} className="mb-2">
            // Edit page ka link jo post ke ID ke sath jata hai
            <Link href={`/edit/${post._id}`} className="text-blue-500 hover:underline">
              {post.title}
            </Link>
          </li>
        ))}
      </ul>
    </div>
  );
}


Kyun: Yeh homepage hai jo app ka main interface hai.
Kahan Use Hoga: Yeh http://localhost:3000 par dikhega.

4. File: components/PostForm.tsx
Kahan Rakhna Hai: /components folder ke andar ek file PostForm.tsx banayein.Kya Karta Hai: Yeh ek reusable form hai jo nayi post banane aur purani post edit karne ke liye use hota hai.
// React ka hook import karein form ke data manage karne ke liye
import { useState } from 'react';
// Sanity client import karein data save karne ke liye
import { client } from '../lib/sanityClient';
// Next.js ka router import karein navigation ke liye
import { useRouter } from 'next/router';
// Post ka type import karein
import { Post } from '../types';

// Form ke props ka type define karein
interface PostFormProps {
  initialData?: Post; // Agar edit mode hai to post ka data milega
}

// PostForm component banayein
export default function PostForm({ initialData }: PostFormProps) {
  // Title ke liye state banayein, agar initialData hai to uska title use karein
  const [title, setTitle] = useState(initialData?.title || '');
  // Content ke liye state banayein, agar initialData hai to uska content use karein
  const [content, setContent] = useState(initialData?.content || '');
  // Router object lein navigation ke liye
  const router = useRouter();

  // Form submit hone par yeh function chalega
  const handleSubmit = async (e: React.FormEvent) => {
    // Page refresh hone se rokein
    e.preventDefault();
    // Post ka object banayein jo Sanity mein save hoga
    const post = { _type: 'post', title, content };
    try {
      // Agar initialData hai, to yeh edit mode hai
      if (initialData) {
        // Existing post ko update karein
        await client.patch(initialData._id).set({ title, content }).commit();
      } else {
        // Nayi post create karein
        await client.create(post);
      }
      // Save hone ke baad homepage par wapas jayein
      router.push('/');
    } catch (error) {
      // Agar error aaye to console mein show karein
      console.error('Post save karne mein error:', error);
    }
  };

  // Form ka HTML render karein
  return (
    // Form ka container
    <div className="container mx-auto p-4">
      // Form element jo submit hone par handleSubmit chalayega
      <form onSubmit={handleSubmit}>
        // Title ka input field
        <div className="mb-4">
          // Title ke liye label
          <label className="block text-sm font-medium">Title</label>
          // Title ka input box
          <input
            type="text"
            value={title}
            onChange={(e) => setTitle(e.target.value)} // Input change hone par state update karein
            className="w-full p-2 border rounded"
            required // Yeh field zaruri hai
          />
        </div>
        // Content ka textarea
        <div className="mb-4">
          // Content ke liye label
          <label className="block text-sm font-medium">Content</label>
          // Content ka textarea
          <textarea
            value={content}
            onChange={(e) => setContent(e.target.value)} // Input change hone par state update karein
            className="w-full p-2 border rounded"
            rows={5} // Textarea ki height set karein
            required // Yeh field zaruri hai
          />
        </div>
        // Submit button
        <button type="submit" className="bg-blue-500 text-white p-2 rounded">
          Post Save Karein
        </button>
      </form>
    </div>
  );
}


Kyun: Yeh form reusable hai, create aur edit dono ke liye kaam karta hai.
Kahan Use Hoga: Create aur edit pages mein.

5. File: pages/create.tsx
Kahan Rakhna Hai: /pages folder ke andar ek file create.tsx banayein.Kya Karta Hai: Yeh page nayi post banane ke liye form dikhata hai.
// PostForm component import karein
import PostForm from '../components/PostForm';

// Create page component banayein
export default function Create() {
  // Page ka HTML render karein
  return (
    // Page ka container
    <div className="container mx-auto p-4">
      // Page ka title
      <h1 className="text-3xl font-bold mb-4">Nayi Post Banayein</h1>
      // PostForm component use karein bina initial data ke
      <PostForm />
    </div>
  );
}


Kyun: Yeh page nayi post create karne ka interface deta hai.
Kahan Use Hoga: http://localhost:3000/create par dikhega.

6. File: pages/edit/[id].tsx
Kahan Rakhna Hai: /pages/edit folder ke andar ek file [id].tsx banayein.Kya Karta Hai: Yeh dynamic page ek specific post ko edit karne ke liye hai.
// Next.js ke static generation functions import karein
import { GetStaticPaths, GetStaticProps } from 'next';
// PostForm component import karein
import PostForm from '../../components/PostForm';
// Sanity client import karein
import { client } from '../../lib/sanityClient';
// Post ka type import karein
import { Post } from '../../types';

// Edit page component banayein
export default function Edit({ post }: { post: Post }) {
  // Page ka HTML render karein
  return (
    // Page ka container
    <div className="container mx-auto p-4">
      // Page ka title
      <h1 className="text-3xl font-bold mb-4">Post Edit Karein</h1>
      // PostForm component use karein post ka data pass karke
      <PostForm initialData={post} />
    </div>
  );
}

// Static paths generate karein dynamic routes ke liye
export const getStaticPaths: GetStaticPaths = async () => {
  // Query jo sab posts ke IDs le
  const query = `*[_type == "post"]{_id}`;
  // Sanity se sab post IDs fetch karein
  const posts = await client.fetch(query);
  // Har post ke liye path banayein
  const paths = posts.map((post: Post) => ({
    params: { id: post._id },
  }));
  // Paths aur fallback option return karein
  return { paths, fallback: 'blocking' };
};

// Static props fetch karein har post ke liye
export const getStaticProps: GetStaticProps = async ({ params }) => {
  // Query jo ek specific post ID se data le
  const query = `*[_type == "post" && _id == $id][0]`;
  // Sanity se post ka data fetch karein
  const post = await client.fetch(query, { id: params?.id });
  // Post ka data props ke tor par return karein
  return { props: { post } };
};


Kyun: Yeh page ek specific post ko edit karne ka interface deta hai.
Kahan Use Hoga: http://localhost:3000/edit/<post-id> par dikhega.

7. File: sanity/schemas/post.ts
Kahan Rakhna Hai: /sanity/schemas folder ke andar ek file post.ts banayein.Kya Karta Hai: Yeh Sanity mein post ka data structure define karta hai.
// Post ka schema define karein
export default {
  name: 'post', // Document ka naam
  title: 'Post', // Sanity Studio mein display naam
  type: 'document', // Yeh ek Sanity document hai
  fields: [
    // Title ka field
    {
      name: 'title', // Field ka naam
      title: 'Title', // Studio mein display naam
      type: 'string', // Data ka type
      validation: (Rule: any) => Rule.required(), // Yeh field zaruri hai
    },
    // Content ka field
    {
      name: 'content', // Field ka naam
      title: 'Content', // Studio mein display naam
      type: 'text', // Data ka type (lamba text ke liye)
      validation: (Rule: any) => Rule.required(), // Yeh field zaruri hai
    },
  ],
};


Kyun: Yeh Sanity ko batata hai ke post ka data kaisa store hoga.
Kahan Use Hoga: Sanity Studio mein post ka structure banane ke liye.

8. File: sanity/sanity.config.ts
Kahan Rakhna Hai: /sanity folder ke andar ek file sanity.config.ts banayein.Kya Karta Hai: Yeh Sanity Studio ka configuration set karta hai.
// Sanity ka config function import karein
import { defineConfig } from 'sanity';
// Sanity Studio ka UI plugin import karein
import { structureTool } from 'sanity/structure';
// Post ka schema import karein
import post from './schemas/post';

// Sanity Studio ka config banayein
export default defineConfig({
  name: 'default', // Studio ka naam
  title: 'Blog Studio', // Studio ka display naam
  projectId: process.env.NEXT_PUBLIC_SANITY_PROJECT_ID, // .env.local se project ID
  dataset: process.env.NEXT_PUBLIC_SANITY_DATASET, // .env.local se dataset
  plugins: [structureTool()], // Studio ka UI enable karein
  schema: {
    types: [post], // Post schema register karein
  },
});


Kyun: Yeh Sanity Studio ko setup karta hai taake aap data manage kar sakein.
Kahan Use Hoga: http://localhost:3333 par Studio chalane ke liye.

9. File: package.json
Kahan Rakhna Hai: Project ke root folder mein package.json banayein.Kya Karta Hai: Yeh project ke dependencies aur scripts define karta hai.
{
  "name": "next-sanity-crud",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev", // Development server chalayein
    "build": "next build", // Production ke liye build karein
    "start": "next start" // Production server chalayein
  },
  "dependencies": {
    "@sanity/client": "^6.4.9", // Sanity API ke liye
    "next": "^14.2.3", // Next.js framework
    "react": "^18.2.0", // React library
    "react-dom": "^18.2.0", // React DOM ke liye
    "typescript": "^5.4.5" // TypeScript ke liye
  },
  "devDependencies": {
    "@types/node": "^20.14.2", // Node.js ke TypeScript types
    "@types/react": "^18.2.0" // React ke TypeScript types
  }
}


Kyun: Yeh project ke liye zaruri packages-silences karta hai.
Kahan Use Hoga: npm install aur npm run dev ke liye.

How to Use (Kaise Use Karein)

Posts Dekhein:

http://localhost:3000 par jayein, sab posts ki list dikhegi.
Har post ka title edit page ka link hai.


Nayi Post Banayein:

Homepage par "Nayi Post Banayein" par click karein.
Form bhar ke "Post Save Karein" click karein.
Post homepage par dikhayi degi.


Post Edit Karein:

Homepage par post ke title par click karein.
Form mein changes karke "Post Save Karein" click karein.


Post Delete Karein:

http://localhost:3333 par Sanity Studio kholein.
Post select karke delete karein, homepage par refresh hone ke baad gayab ho jayega.


Sanity Studio Mein Data Manage Karein:

Studio mein posts add, edit, ya delete karein.
Changes real-time mein app mein dikhein ge.



Troubleshooting (Agar Problem Aaye)

"Invalid projectId" Error:
.env.local mein NEXT_PUBLIC_SANITY_PROJECT_ID check karein.


Posts Nahi Dikh Rahe:
Dataset production hai ya nahi check karein.
API token ke write permissions check karein.


TypeScript Errors:
npm install dobara chalaein.


Sanity Studio Nahi Chal Raha:
/sanity folder mein npm install aur npm run dev dobara chalaein.



Contributing (Kaise Madad Karein)
Agar aap is project mein kuch add karna chahte hain:

Repository fork karein.
Apne changes add karein.
Pull request bhejein.
